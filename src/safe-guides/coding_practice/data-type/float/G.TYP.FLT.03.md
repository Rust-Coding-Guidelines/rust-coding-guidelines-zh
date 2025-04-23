## G.TYP.FLT.03 对精度高要求的场景下，使用浮点数进行运算和比较时需要注意精度损失

**【级别】** 建议

**【描述】**

浮点数计算通常都是不精确的，直接对浮点数进行运算和比较可能造成数据错误。 如何更好地处理浮点数，可以参阅 [浮点数指南](https://floating-point-gui.de/) 。

但是对精度要求不高的场合，比如机器学习中某些场景，对此不做要求。

**【反例】**

```rust
#![warn(clippy::float_arithmetic, clippy::float_cmp, clippy::float_cmp_const)]

fn main(){
    let x = 1.2331f64;
    let y = 1.2332f64;

    payment(x, y);
}

fn payment(x: f64, y: f64) -> f64{
    // 不符合： 浮点数计算有精度损失
    y - x
}

```

**【正例】**

推荐使用精度更高的类型，比如 Decimal 类型（需要第三方库支持）。

```rust
#![warn(clippy::float_arithmetic, clippy::float_cmp, clippy::float_cmp_const)]

fn main(){
    let x = 1.2331f64;
    let y = 1.2332f64;

    payment(x, y);
}

fn payment(x: f64, y: f64) -> Result<f64, PaymentErr>{
    let z = y - x;
    let error_margin = f64::EPSILON;
    // 符合：浮点数的差异绝对值在允许范围内
    if z.abs() < error_margin {
        return Ok(z);
    } else {
        return PaymentErr(e);
    }
}
```


**【Lint 检测】**

| lint name                                                                                                | Clippy 可检测 | Rustc 可检测 | Lint Group  | 默认level |
| -------------------------------------------------------------------------------------------------------- | ------------- | ------------ | ----------- | --------- |
| [float_arithmetic](https://rust-lang.github.io/rust-clippy/master/#float_arithmetic)                     | yes           | no           | restriction | allow     |
| [float_cmp](https://rust-lang.github.io/rust-clippy/master/#float_cmp)                                   | yes           | no           | pedantic    | allow     |
| [float_cmp_const](https://rust-lang.github.io/rust-clippy/master/#float_cmp_const)                       | yes           | no           | restriction | allow     |
| [float_equality_without_abs](https://rust-lang.github.io/rust-clippy/master/#float_equality_without_abs) | yes           | no           | suspicious  | warn      |



