## G.TYP.SCT.01 对外导出的公开的 Struct，宜添加`#[non_exhaustive]`属性

**【级别】** 建议

**【描述】**

作为对外公开的 结构体，为了保持稳定性，应该使用 `#[non_exhaustive]`属性，避免因为将来结构体字段发生变化而影响到下游的使用。主要涉及命名结构体和元组结构体。

**【反例】**

在 `#[non_exhaustive]` 属性稳定之前，社区内还有一种约定俗成的写法来达到防止下游自定义枚举方法。通过 `manual_non_exhaustive` 可以监控这类写法。

```rust
#![warn(clippy::exhaustive_structs)]

struct S {
    pub a: i32,
    pub b: i32,
    _priv: (),  // 不符合：这里用 下划线作为前缀定义的字段，作为私有字段，不对外公开
}

// 用户无法自定义实现该结构体的方法。
```

**【正例】**

```rust
#![warn(clippy::exhaustive_structs)]

// 符合
#[non_exhaustive]
struct Foo {
    bar: u8,
    baz: String,
}
```

**【例外】**

也有例外情况！

从语义角度看，`#[non_exhaustive]` 只是代表未穷尽的字段或枚举变体，是为了表达“未来可能有变化”这种语义。

但是当要表达 “这个结构体不允许对方实例化” 的语义时，通过自定义的 `_priv`字段就可以更好地表达这个语义。

而使用 `#[non_exhaustive]` 虽然也能达到 “不允许对方实例化的目的”，但是在代码可读性层面，却无法表达出这个语义。

```rust
// From: https://github.com/tokio-rs/tokio/blob/master/tokio-util/src/codec/framed.rs

#[allow(clippy::manual_non_exhaustive)]
pub struct FramedParts<T, U> {
    pub io: T,
    pub codec: U,
    pub read_buf: BytesMut,
    pub write_buf: BytesMut,
    
    /// This private field allows us to add additional fields in the future in a
    /// backwards compatible way.
    _priv: (),
}

impl<T, U> FramedParts<T, U> {
    /// Create a new, default, `FramedParts`
    pub fn new<I>(io: T, codec: U) -> FramedParts<T, U>
    where
        U: Encoder<I>,
    {
        FramedParts {
            io,
            codec,
            read_buf: BytesMut::new(),
            write_buf: BytesMut::new(),
            _priv: (),
        }
    }
}
```

**【Lint 检测】**

| lint name                                                                                      | Clippy 可检测 | Rustc 可检测 | Lint Group  | 默认level |
| ---------------------------------------------------------------------------------------------- | ------------- | ------------ | ----------- | --------- |
| [exhaustive_structs](https://rust-lang.github.io/rust-clippy/master/#exhaustive_structs)       | yes           | no           | restriction | allow     |
| [manual_non_exhaustive](https://rust-lang.github.io/rust-clippy/master/#manual_non_exhaustive) | yes           | no           | style       | warn      |


