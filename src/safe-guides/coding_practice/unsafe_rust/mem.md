#  内存

这里指 Unsafe Rust 下的数据布局、内存管理和使用相关规范。

---

## P.UNS.MEM.01   要注意选择合适的结构体、元组、枚举的数据布局

**【描述】**

Rust 中结构体和元组 ，编译器会随意重排其字段来优化布局。请根据具体的场景来选择合适的数据布局。

可以通过 以下`#[repr]` 属性来控制结构体和元组的数据布局：

- `#[repr(Rust)]` ，默认 Rust 数据布局
- `#[repr(C)]` ，与 C 兼容的布局
- `#[repr(align(N))]` ，指定对齐方式
- `#[repr(packed)]` ，指定字段将不在内部对齐
- `#[repr(transparent)]` ，让包含单个字段的结构体布局和其字段相同

可以通过 以下`#[repr]` 属性来控制枚举体的数据布局：

- 特定整数类型
  - `#[repr(u8)]`
  - `#[repr(u16)]`
  - `#[repr(u32)]`
  - `#[repr(u64)]`
  - `#[repr(i8)]`
  - `#[repr(i16)]`
  - `#[repr(i32)]`
  - `#[repr(i64)]`
- C 兼容布局
  - `#[repr(C)]`
- 指定判别式大小的 C 兼容布局
  - `#[repr(C, u8)]`
  - `#[repr(C, u16)]`
  - 以此类推

枚举需要注意的地方：

- 枚举不允许通过`#[repr(align)] ` 手动指定对齐方式。
- 空枚举不能使用 `repr` 属性
- 无字段枚举不允许指定判别式大小的 C 兼容布局，比如 `[repr(C, Int)]`
- 数据承载（有字段）枚举则允许所有类型的 `repr`属性

---



## G.UNS.MEM.01   使用 `MaybeUninit<T>` 来处理未初始化的内存

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [uninit_assumed_init](https://rust-lang.github.io/rust-clippy/master/#uninit_assumed_init) | yes           | no           | correctness | deny  |
| [uninit_vec](https://rust-lang.github.io/rust-clippy/master/#uninit_vec) | yes           | no           | correctness | deny  |

### 【描述】

 Rust 编译器要求变量要根据其类型正确初始化。

比如引用类型的变量必须对齐且非空。这是一个必须始终坚持的不变量，即使在 Unsafe 代码中也是如此。因此，零初始化引用类型的变量会导致立即未定义行为，无论该引用是否访问过内存。

编译器利用这一点，进行各种优化，并且可以省略运行时检查。

使用前请仔细查看 `MaybeUninit<T>` 相关文档。

【正例】

```rust
use std::mem::MaybeUninit;

let mut x = MaybeUninit::<bool>::uninit();
x.write(true); // 这里正确进行了初始化
let x_init = unsafe { x.assume_init() }; // 通过 assume_init 对 MaybeUninit 的内存取值
assert_eq!(x_init, true);

// 下面数组应该是可以的
let _: [MaybeUninit<bool>; 5] = unsafe {
    MaybeUninit::uninit().assume_init()
};

// Vec 未初始化内存正确处理
let mut vec: Vec<u8> = vec![0; 1000];
reader.read(&mut vec);
// or
let mut vec: Vec<MaybeUninit<T>> = Vec::with_capacity(1000);
vec.set_len(1000);  // `MaybeUninit` can be uninitialized
// or
let mut vec: Vec<u8> = Vec::with_capacity(1000);
let remaining = vec.spare_capacity_mut();  // `&mut [MaybeUninit<u8>]`
// perform initialization with `remaining`
vec.set_len(...);  // Safe to call `set_len()` on initialized part
```

【反例】

由调用者来保证`MaybeUninit<T>`确实处于初始化状态。当内存尚未完全初始化时调用 `assume_init()` 会导致立即未定义的行为。

```rust
use std::mem::{self, MaybeUninit};
// 零初始化引用
let x: &i32 = unsafe { mem::zeroed() }; // undefined behavior! ⚠️
// The equivalent code with `MaybeUninit<&i32>`:
let x: &i32 = unsafe { MaybeUninit::zeroed().assume_init() }; // undefined behavior! 
// 布尔值必须初始化
let b: bool = unsafe { mem::uninitialized() }; // undefined behavior! ⚠️
// The equivalent code with `MaybeUninit<bool>`:
let b: bool = unsafe { MaybeUninit::uninit().assume_init() }; // undefined behavior! 
// 整数类型也必须初始化
let x: i32 = unsafe { mem::uninitialized() }; // undefined behavior! ⚠️
// The equivalent code with `MaybeUninit<i32>`:
let x: i32 = unsafe { MaybeUninit::uninit().assume_init() }; 

// Vec未初始化内存使用 set_len 是未定义行为
let mut vec: Vec<u8> = Vec::with_capacity(1000);
unsafe { vec.set_len(1000); }
reader.read(&mut vec); // undefined behavior!
```

【例外】

在能保证` MaybeUninit` 不需要初始化的情况下使用 `assume_init` 是安全的。

```rust
pub unsafe trait Array: Sized {
  
    /// Same array but item is wrapped with
    /// [`MaybeUninit<_>`](core::mem::MaybeUninit).
    /// ```
    /// # use arraylib::Array; fn dummy<T>() where
    /// [T; 4]: Array<Item = T, Maybe = [core::mem::MaybeUninit<T>; 4]>
    /// # {}
    /// ```
    type Maybe: Array<Item = MaybeUninit<Self::Item>>;

    /// [`MaybeUninit<T>`]: core::mem::MaybeUninit
    #[inline]
    // Initializing generic type with uninitialized state seems insane, but is
    // unsafe trait and `Array` guarantees that it's an array. And `Array::Maybe`
    // is an array of `MaybeUninit` that doesn't require initialization, so
    // everything is ok
    // 这里是一个数组，可以保证不需要去初始化
    #[allow(clippy::uninit_assumed_init)]
    fn uninit() -> Self::Maybe {
        unsafe {
            // ## Safety
            //
            // Completely safe as `MaybeUninit` don't require initialization
            MaybeUninit::uninit().assume_init()
        }
    }
}
```

## P.UNS.MEM.02 不能修改其它进程/动态库的存变量

### 【级别：必须】

必须按此规范执行。

### 【描述】

不要尝试修改其它进程/动态库的内存数据，否则会出现内存段错误(SIGSEGV)。

【反例】

`sqlite3_libversion()` 返回的 sqlite 版本信息指针指向 `/usr/lib/libsqlite3.so` 动态库的 static 字符串。

libsqlite3.so 中分配的静态字符串不属于进程的内存范围中。

当进程尝试修改 sqlite 动态库的静态字符串内容，操作系统就会发送 SIGSEGV 信号终止进程，以保证 sqlite 动态库的内存数据安全。

```rust
#[link(name = "sqlite3")]
extern "C" {
    fn sqlite3_libversion() -> *mut std::os::raw::c_char;
}

fn edit_sqlite_version() {
    unsafe {
        let mut sqlite_version = sqlite3_libversion();
        // SIGSEGV: invalid memory reference
        *sqlite_version = 3;
    }
}
```

## P.UNS.MEM.03 不能让 String/Vec 自动 Drop 其它进程/动态库的内存数据

### 【级别：必须】

必须按此规范执行。

### 【描述】

使用 String/Vec 指向其它进程/动态库的内存数据时，一定要手动禁止 String/Vec 的 Drop 方法(析构函数)的调用，避免 free 其它进程/动态库的内存数据。

【反例】

`sqlite3_libversion()` 返回的 sqlite 版本信息指针指向 `/usr/lib/libsqlite3.so` 动态库的 static 字符串。

当进程在 String drop 的时候尝试释放 sqlite 动态库的静态字符串内存时，操作系统就会发送 SIGABRT 信号终止进程，以保证 sqlite 动态库的内存数据安全。

```rust
#[link(name = "sqlite3")]
extern "C" {
    fn sqlite3_libversion() -> *mut std::os::raw::c_char;
}

fn print_sqlite_version() {
    unsafe {
        let ptr = sqlite3_libversion();
        let len = libc::strlen(ptr);
        let version = String::from_raw_parts(ptr.cast(), len, len);
        println!("found sqlite3 version={}", version);
        // SIGABRT: invalid free
    }
}
```

【正例】

除了用 mem::forget 或者 ManualDrop 禁止 String drop 其它动态库的内存，也可以用标准库 ptr/slice 的 copy 或者 `libc::strdup` 将 sqlite 的版本信息字符串**复制到当前进程的内存空间**再进行操作

```rust
fn print_sqlite_version() {
    unsafe {
        let ptr = sqlite3_libversion();
        let len = libc::strlen(ptr);
        let version = String::from_raw_parts(ptr.cast(), len, len);
        println!("found sqlite3 version={}", version);
        // 手动禁止 String 的析构函数调用
        std::mem::forget(version);
    }
}
```

## P.UNS.MEM.04 尽量用可重入(reentrant)版本的 C API/系统调用

### 【级别：必须】

必须按此规范执行。

### 【描述】

以 Linux 系统为例，在 **glibc**(/usr/lib/libc.so) 等知名 C 语言库中，

很多 API 会既提供不可重入版本和**可重入(reentrant)**版本，例如 ctime 和 ctime_r 这对系统调用。

可重入版本的函数命名一般带 **_r** 的后缀，*_r* 也就是单词可重入 reentrant 的缩写。

libc 中不可重入函数的执行过程一般是将函数的输出写到动态库的某个 static 命令内，然后再返回指向该 static 变量的指针返回给调用方，因此是一种「有状态」的函数，多线程环境下可能有**线程安全问题**。

例如线程 A 正在将 glibc 动态库的 gmtime 数据逐个复制回来，结果复制到一半线程 B 调用 gmtime 把后半部分的 gmtime 输出数据给更新掉了导致线程 A 得到的数据有误。

而无重入版本例如 libc::localtime_r 会比 libc::localtime 多一个入参叫 result，

允许调用方进程的内存空间内分配内存，再将调用方进程的可变指针传入到 glibc 中让 glibc 修改可知指针指向的数据。

应当通过工具搜索动态库的函数符号查找可重入版本的函数，或者通过 man 文档查询自己所用函数有没有可重入的版本。

```
[w@ww repos]$ nm -D /usr/lib/libc.so.6 | grep "_r@"
00000000000bb030 W asctime_r@@GLIBC_2.2.5
00000000000bb100 T ctime_r@@GLIBC_2.2.5
0000000000040a30 T drand48_r@@GLIBC_2.2.5
```

使用不可重入函数的危害例如 P.UNS.MEM.02 和 P.UNS.MEM.03 规范的反例中的 sqlite3_libversion() 会导致开发人员带来很大的心智负担，需要人工 code review 确保没有线程安全和内存安全问题，因此必须尽量使用可重入版本的函数。

【正例】

chrono 库中用 libc::localtime_r 获取本地时间而不用 libc::localtime

ctime_r, gmtime_r, localtime_r, gethostbyname_r

【反例】

ctime, gmtime, localtime, gethostbyname
