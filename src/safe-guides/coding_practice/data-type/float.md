# 浮点数

Rust 的浮点数包括 `f32` 和 `f64` 两种类型。Rust 编译器默认推断的 Float 类型是 `f64`。

---

## G.TYP.Float.01    使用 `f32` 字面量时，小心被 Rust 编译器截断

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [excessive_precision](https://rust-lang.github.io/rust-clippy/master/#excessive_precision) | yes           | no           | style      | warn  |

### 【描述】

当指定超过 `f32` 精度的字面量值时，Rust 会默认截断该值。

【正例】

```rust
let v: f64 = 0.123_456_789_9;
println!("{}", v); //  0.123_456_789_9
```

【反例】

```rust
let v: f32 = 0.123_456_789_9;
println!("{}", v); //  0.123_456_789
```

