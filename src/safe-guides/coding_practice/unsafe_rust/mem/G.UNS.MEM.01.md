## G.UNS.MEM.01   使用 `MaybeUninit<T>` 来处理未初始化的内存

**【级别】** 要求

**【描述】**

 Rust 编译器要求变量要根据其类型正确初始化。

比如引用类型的变量必须对齐且非空。这是一个必须始终坚持的不变量，即使在 Unsafe 代码中也是如此。因此，零初始化引用类型的变量会导致立即未定义行为，无论该引用是否访问过内存。

编译器利用这一点，进行各种优化，并且可以省略运行时检查。

使用前请仔细查看 `MaybeUninit<T>` 相关文档。

**【反例】**

由调用者来保证`MaybeUninit<T>`确实处于初始化状态。当内存尚未完全初始化时调用 `assume_init()` 会导致立即未定义的行为。

```rust

use std::mem::{self, MaybeUninit};
// 不符合：零初始化引用
let x: &i32 = unsafe { mem::zeroed() }; // undefined behavior! ⚠️
// 等价于 `MaybeUninit<&i32>`:
let x: &i32 = unsafe { MaybeUninit::zeroed().assume_init() }; // undefined behavior! 
// 不符合：布尔值必须初始化
let b: bool = unsafe { mem::uninitialized() }; // undefined behavior! ⚠️
// 等价于 `MaybeUninit<bool>`:
let b: bool = unsafe { MaybeUninit::uninit().assume_init() }; // undefined behavior! 
// 不符合：整数类型也必须初始化
let x: i32 = unsafe { mem::uninitialized() }; // undefined behavior! ⚠️
// 等价于 `MaybeUninit<i32>`:
let x: i32 = unsafe { MaybeUninit::uninit().assume_init() }; 

// 不符合：Vec未初始化内存使用 set_len 是未定义行为
let mut vec: Vec<u8> = Vec::with_capacity(1000);
unsafe { vec.set_len(1000); }
reader.read(&mut vec); // undefined behavior!
```

**【正例】**

```rust
use std::mem::MaybeUninit;

let mut x = MaybeUninit::<bool>::uninit();
x.write(true); // 符合：这里正确进行了初始化
let x_init = unsafe { x.assume_init() }; // 通过 assume_init 对 MaybeUninit 的内存取值
assert_eq!(x_init, true);

// 符合：下面数组应该是可以的
let _: [MaybeUninit<bool>; 5] = unsafe {
    MaybeUninit::uninit().assume_init()
};

// 符合：Vec 未初始化内存正确处理
let mut vec: Vec<u8> = vec![0; 1000];
reader.read(&mut vec);
// 符合
let mut vec: Vec<MaybeUninit<T>> = Vec::with_capacity(1000);
vec.set_len(1000);  // `MaybeUninit` can be uninitialized
// 符合：
let mut vec: Vec<u8> = Vec::with_capacity(1000);
let remaining = vec.spare_capacity_mut();  // `&mut [MaybeUninit<u8>]`
// perform initialization with `remaining`
vec.set_len(...);  // Safe to call `set_len()` on initialized part
```

**【例外】**

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

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [uninit_assumed_init](https://rust-lang.github.io/rust-clippy/master/#uninit_assumed_init) | yes           | no           | correctness | deny  |
| [uninit_vec](https://rust-lang.github.io/rust-clippy/master/#uninit_vec) | yes           | no           | correctness | deny  |
