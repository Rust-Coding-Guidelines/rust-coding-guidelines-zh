# 浮点数

Rust 的浮点数包括 `f32` 和 `f64` 两种类型。Rust 编译器默认推断的 Float 类型是 `f64`。

---
<!-- toc -->
---

## G.TYP.Float.01    使用`f32`字面量时，避免被Rust编译器截断

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


## G.TYP.Float.02  从任何数字类型转换为`f64`类型时注意避免损失精度

**【级别】** 建议

**【描述】**

这种转换可能会有值的舍入错误发生。在某些对于精度要求比较高的场景需要注意。

**【示例】**

```rust
let x = u64::MAX;
x as f64; // 18446744073709551615
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [cast_precision_loss](https://rust-lang.github.io/rust-clippy/master/#cast_precision_loss) | yes           | no           | pedantic   | allow |


## G.TYP.Float.03  对精度高要求的场景下，不应使用浮点数进行运算和比较

**【级别】** 建议

**【描述】**

 浮点数计算通常都是不精确的，直接对浮点数进行运算和比较可能造成数据错误。 如何更好地处理浮点数，可以参阅 [浮点数指南](https://floating-point-gui.de/) 。

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



## G.TYP.Float.04  宜使用Rust内置方法处理浮点数计算

**【级别】** 建议

**【描述】**

内置方法会牺牲一定性能，但可以提升准确性。

**【反例】**

```rust
let a = 3f32;
let _ = a.powf(1.0 / 3.0);
let _ = (1.0 + a).ln();
let _ = a.exp() - 1.0;

use std::f32::consts::E;

let a = 3f32;
let _ = (2f32).powf(a);
let _ = E.powf(a);
let _ = a.powf(1.0 / 2.0);
let _ = a.log(2.0);
let _ = a.log(10.0);
let _ = a.log(E);
let _ = a.powf(2.0);
let _ = a * 2.0 + 4.0;
let _ = if a < 0.0 {
    -a
} else {
    a
};
let _ = if a < 0.0 {
    a
} else {
    -a
};
```

**【正例】**

```rust
let a = 3f32;
let _ = a.cbrt();
let _ = a.ln_1p();
let _ = a.exp_m1();

use std::f32::consts::E;

let a = 3f32;
let _ = a.exp2();
let _ = a.exp();
let _ = a.sqrt();
let _ = a.log2();
let _ = a.log10();
let _ = a.ln();
let _ = a.powi(2);
let _ = a.mul_add(2.0, 4.0);
let _ = a.abs();
let _ = -a.abs();
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [imprecise_flops](https://rust-lang.github.io/rust-clippy/master/#imprecise_flops) | yes           | no           | nursery    | allow |
| [suboptimal_flops](https://rust-lang.github.io/rust-clippy/master/#suboptimal_flops) | yes           | no           | nursery    | allow |

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


## G.TYP.Float.06 禁止在浮点数和整数相互转换时使用 `transmute` 

**【级别】** 要求

**【描述】**

略

**【反例】**

```rust
unsafe {
    let _: u32 = std::mem::transmute(1f32);
    let _: f32 = std::mem::transmute(1_u32); // where x: u32
}
```

**【正例】**

```rust
let _: f32 = f32::from_bits(1_u32);
let _: u32 = 1f32.to_bits();
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [transmute_float_to_int](https://rust-lang.github.io/rust-clippy/master/#transmute_float_to_int) | yes           | no           | complexity | warn  |
| [transmute_int_to_float](https://rust-lang.github.io/rust-clippy/master/#transmute_int_to_float) | yes           | no           | complexity | warn  |

