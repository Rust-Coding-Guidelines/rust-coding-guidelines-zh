## G.TYP.FLT.04  宜使用Rust内置方法处理浮点数计算

**【级别】** 建议

**【描述】**

内置方法可能会牺牲一定性能，但可以提升准确性。

**【反例】**

```rust
#![warn(clippy::imprecise_flops, clippy::suboptimal_flops)]

// 不符合
fn main() {
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
}
```

**【正例】**

```rust
#![warn(clippy::imprecise_flops, clippy::suboptimal_flops)]

// 符合
fn main(){
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
}

```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [imprecise_flops](https://rust-lang.github.io/rust-clippy/master/#imprecise_flops) | yes           | no           | nursery    | allow |
| [suboptimal_flops](https://rust-lang.github.io/rust-clippy/master/#suboptimal_flops) | yes           | no           | nursery    | allow |

