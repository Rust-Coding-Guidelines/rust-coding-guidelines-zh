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

