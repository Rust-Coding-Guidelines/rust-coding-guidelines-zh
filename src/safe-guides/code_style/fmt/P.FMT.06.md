## P.FMT.06  当有多行表达式操作时，操作符应该置于行首

**【描述】**

当有多行表达式操作时，操作符应该置于行首，这样有利于代码的可读性和可维护性。

**【反例】**

操作符置于行尾：

```rust
fn main() {
    // 不符合
    let or = foofoofoofoofoofoofoofoofoofoofoofoofoofoofoofoo ||
        barbarbarbarbarbarbarbarbarbarbarbarbarbarbarbar;
    // 不符合
    let sum = 123456789012345678901234567890 +
        123456789012345678901234567890 +
        123456789012345678901234567890;
    // 不符合
    let range = aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa..
        bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb;
}
```

**【正例】**

操作符置于行首：

```rust
fn main() {
    // 符合
    let or = foofoofoofoofoofoofoofoofoofoofoofoofoofoofoofoo
        || barbarbarbarbarbarbarbarbarbarbarbarbarbarbarbar;

    // 符合
    let sum = 123456789012345678901234567890
        + 123456789012345678901234567890
        + 123456789012345678901234567890;
    // 符合
    let range = aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
        ..bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb;
}
```

**【rustfmt 配置】**

| 对应选项                                                                   | 可选值        | 是否 stable | 说明                   |
| -------------------------------------------------------------------------- | ------------- | ----------- | ---------------------- |
| [`binop_separator`](https://rust-lang.github.io/rustfmt/?#binop_separator) | Front（默认） | No          | 换行后，操作符置于行首 |
