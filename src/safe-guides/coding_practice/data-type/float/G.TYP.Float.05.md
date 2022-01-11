## G.TYP.Float.05  使用字面量定义浮点数时，尽量使用 `f64` 类型而非`f32`类型

**【级别】** 建议

**【描述】**

在 `f32` 浮点数字面量在定义时，将会损失精度，应该尽量使用 `f64` 类型。

**【反例】**

```rust
let x : f32 = 16_777_217.0;
assert_eq!(16777216.0, x);
```

**【正例】**

```rust
let x : f64 = 16_777_217.0;
assert_eq!(16777217.0, x);
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [imprecise_flops](https://rust-lang.github.io/rust-clippy/master/#imprecise_flops) | yes           | no           | nursery    | allow |


