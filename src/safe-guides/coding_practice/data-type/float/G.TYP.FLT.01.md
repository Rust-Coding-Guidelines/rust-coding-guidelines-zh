## G.TYP.FLT.01 使用浮点数字面量时，要警惕是否存在被Rust编译器截断的风险

**【级别】** 建议

**【描述】**

当指定超过类型精度（`f32` 或 `f64`）的字面量值时，Rust 会默认截断该值。

**【反例】**

```rust
// 不符合
let v: f32 = 0.123_456_789_9;
println!("{}", v); //  0.123_456_789
```

**【正例】**

```rust
// 符合
let v: f64 = 0.123_456_789_9;
println!("{}", v); //  0.123_456_789_9
```

**【Lint 检测】**

| lint name                                                                                  | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认level |
| ------------------------------------------------------------------------------------------ | ------------- | ------------ | ---------- | --------- |
| [excessive_precision](https://rust-lang.github.io/rust-clippy/master/#excessive_precision) | yes           | no           | style      | warn      |


