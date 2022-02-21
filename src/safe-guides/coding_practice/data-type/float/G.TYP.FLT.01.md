## G.TYP.FLT.01 使用浮点数字面量时，避免被Rust编译器截断

**【级别】** 建议

**【描述】**

当指定超过 `f32` 精度的字面量值时，Rust 会默认截断该值。

**【反例】**

```rust
let v: f32 = 0.123_456_789_9;
println!("{}", v); //  0.123_456_789
```

**【正例】**

```rust
let v: f64 = 0.123_456_789_9;
println!("{}", v); //  0.123_456_789_9
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [excessive_precision](https://rust-lang.github.io/rust-clippy/master/#excessive_precision) | yes           | no           | style      | warn  |


