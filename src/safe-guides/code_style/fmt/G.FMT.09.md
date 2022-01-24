## G.FMT.09  当有多行表达式操作时，操作符应该置于行首

**【级别】** 建议

**【描述】**

当有多行表达式操作时，操作符应该置于行首。

**【反例】**

操作符置于行尾

```rust
fn main() {
    let or = foofoofoofoofoofoofoofoofoofoofoofoofoofoofoofoo ||
        barbarbarbarbarbarbarbarbarbarbarbarbarbarbarbar;

    let sum = 123456789012345678901234567890 +
        123456789012345678901234567890 +
        123456789012345678901234567890;

    let range = aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa..
        bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb;
}
```

**【正例】**

操作符置于行首

```rust
fn main() {
    let or = foofoofoofoofoofoofoofoofoofoofoofoofoofoofoofoo
        || barbarbarbarbarbarbarbarbarbarbarbarbarbarbarbar;

    let sum = 123456789012345678901234567890
        + 123456789012345678901234567890
        + 123456789012345678901234567890;

    let range = aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
        ..bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb;
}
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`binop_separator`](https://rust-lang.github.io/rustfmt/?#binop_separator) | Front（默认） | No| 换行后，操作符置于行首 |
