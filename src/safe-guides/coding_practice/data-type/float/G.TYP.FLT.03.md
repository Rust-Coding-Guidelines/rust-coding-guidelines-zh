## G.TYP.FLT.03  对精度高要求的场景下，使用浮点数进行运算和比较时需要注意

**【级别】** 建议

**【描述】**

浮点数计算通常都是不精确的，直接对浮点数进行运算和比较可能造成数据错误。 如何更好地处理浮点数，可以参阅 [浮点数指南](https://floating-point-gui.de/) 。

但是对精度要求不高的场合，比如机器学习中某些场景，对此不做要求。

**【反例】**

```rust
let x = 1.2331f64;
let y = 1.2332f64;

if y == 1.23f64 { }
if y != x {} // where both are floats

// or
pub fn is_roughly_equal(a: f32, b: f32) -> bool {
    (a - b) < f32::EPSILON
}
```

**【正例】**

```rust
let x = 1.2331f64;
let y = 1.2332f64;

let error_margin = f64::EPSILON; // Use an epsilon for comparison
// Or, if Rust <= 1.42, use `std::f64::EPSILON` constant instead.
// let error_margin = std::f64::EPSILON;
if (y - 1.23f64).abs() < error_margin { }
if (y - x).abs() > error_margin { }

// or
pub fn is_roughly_equal(a: f32, b: f32) -> bool {
    (a - b).abs() < f32::EPSILON
}
```


**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [float_arithmetic](https://rust-lang.github.io/rust-clippy/master/#float_arithmetic) | yes           | no           | restriction | allow |
| [float_cmp](https://rust-lang.github.io/rust-clippy/master/#float_cmp) | yes           | no           | pedantic    | allow |
| [float_cmp_const](https://rust-lang.github.io/rust-clippy/master/#float_cmp_const) | yes           | no           | restriction | allow |
| [float_equality_without_abs](https://rust-lang.github.io/rust-clippy/master/#float_equality_without_abs) | yes           | no           | suspicious  | warn  |



