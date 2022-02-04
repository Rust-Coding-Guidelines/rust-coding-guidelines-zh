# Float Cheat Sheet

> From: [https://github.com/brazzy/floating-point-gui.de](https://github.com/brazzy/floating-point-gui.de)

## 浮点数类型

Rust 有  [IEEE 754](https://floating-point-gui.de/formats/fp/) 单精度  (32-bit) 和  双精度 (64-bit) 类型:

```rust
	let x: f32 = 0.1; // 32-bit float
	let y: f64 = 0.1; // 64-bit float
```

默认的浮点数类型是 `f64`:

```rust
	let z = 0.1; // 64-bit float
```

## Decimal Types

Rust 没有内建的 Decimal 类型，但是有第三方库  [rust_decimal](https://crates.io/crates/rust-decimal) 来支持 Decimal 类型。该库实现了 128-bit [有限精度的（limited-precision）](https://floating-point-gui.de/formats/exact/)  关键字  `Decimal` 表示 Decimal 类型:

```rust
use rust_decimal::prelude::*;

let a = Decimal::new(1, 1); // second param is the number of fractional digits
let b = Decimal::new(2, 1); // a Decimal representing exactly 0.2
let c = a + b; // a Decimal representing exactly 0.3
```

## 如何四舍五入

生成字符串:

```rust
format!("{:.2}", 1.2399); // returns "1.24"
format!("{:.3}", 1.2399); // returns "1.240"
format!("{:.2}", 1.2); // returns "1.20"
```

打印标准输出:

```rust
println!("{:.2}", 1.2399); // prints "1.24"
```

这个 `round` 方法返回与数字最接近的整数。它使用  [四舍五入模式（rounding mode）](https://floating-point-gui.de/errors/rounding/) ，"从零开始四舍五入"，并且对`f32`和`f64`类型都有效。

```rust
let f: f64 = 3.3;
let g: f64 = -3.3;

f.round(); // returns 3.0
g.round(); // returns -3.0
```

`rust_decimal` crate 包含`round_dp`方法，它使用Banker的[舍入模式](https://floating-point-gui.de/errors/rounding/)。

```rust
let pi = Decimal::from_str("3.1415926535897932384626433832").unwrap();
println!("{}", pi.round_dp(2).to_string()); // prints "3.14"
```

`rust_decimal` crate 还包含`round_dp_with_strategy`方法，允许你指定一个四舍五入策略。

```rust
let i = Decimal::from_str("1.25").unwrap();
println!(
    "{}",
    i.round_dp_with_strategy(1, RoundingStrategy::RoundDown)
        .to_string()
) // prints "1.2"
```

## Resources

- [rust_decimal crate](https://crates.io/crates/rust-decimal) 