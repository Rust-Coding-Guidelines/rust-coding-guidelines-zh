## P.MOD.01  合理控制对外接口和模块之间的可见性

**【描述】**

Rust提供强大的模块（module）系统，并且可以管理这些模块之间的可见性（公有（public）或私有（private））。

1、对于提供给其他crate使用的对外函数、结构体、trait等类型需要严格控制对外pub的范围，避免将内部成员对外提供。

2、对于crate内部，mod之间可见的类型，需要添加上`pub(crate)`。

3、对于mod内部私有的类型，不要添加`pub(crate)`或者`pub`。

**【正例】**

```rust
// lib.rs
pub mod sha512;
pub use sha512::Sha512;

// sha512.rs
pub struct Sha512 {
    inner: Sha512Inner, // inner作为内部结构体，不添加pub描述
}

```
