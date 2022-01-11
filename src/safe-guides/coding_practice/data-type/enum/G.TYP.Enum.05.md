## G.TYP.Enum.05 对外导出的公开Enum，宜添加`#[non_exhaustive]`属性

**【级别】** 建议

**【描述】**

作为对外公开的 Enum，为了保持稳定性，应该使用 `#[non_exhaustive]`属性，避免因为将来Enum 枚举变体的变化而影响到下游的使用。


**【反例】**

在 `#[non_exhaustive]` 属性稳定之前，社区内还有一种约定俗成的写法来达到防止下游自定义枚举方法。通过 `manual_non_exhaustive` 可以监控这类写法。

```rust
enum E {
    A,
    B,
    #[doc(hidden)]
    _C, // 这里用 下划线作为前缀定义的变体，作为隐藏的变体，不对外展示
}
// 用户无法自定义实现该 枚举的方法，达到一种稳定公开枚举的目的。
```

**【正例】**

```rust
#[non_exhaustive]
enum E {
    A,
    B,
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [exhaustive_enums](https://rust-lang.github.io/rust-clippy/master/#exhaustive_enums) | yes           | no           | restriction | allow |
| [manual_non_exhaustive](https://rust-lang.github.io/rust-clippy/master/#manual_non_exhaustive) | yes           | no           | style       | warn  |



