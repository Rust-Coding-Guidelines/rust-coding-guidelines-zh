# 整数

Rust 中有目前有 十二种整数类型：`i8/u8`, `i16/u16`, `i32/u32`, `i64/u64`, `i128/u128`, `isize/usize` 。

---

## P.TYP.INT.01  在用整数计算的时候需要考虑整数溢出、回绕和截断的风险

【描述】

需要结合场景和业务来考虑如果发生溢出、回绕或截断的时候，是否会引起严重的问题。

比如，对于时间要求精准的系统，如果在计算时间发生整数溢出，或者去计算某个数组的索引等，那可能会发生严重问题。但如果你只是一个简单的计算器，不会被用到具体的业务场合，那溢出也没有关系，因为你只需要在合理的数字范围内计算性能最好。

在 Rust 标准库中，提供 `add`/ `checked_add` / ` saturating_add`/`overflowing_add` / `wrapping_add` 不同系列方法，返回值不同，根据不同的场合选择适合的方法。

1. `check_*`函数返回`Option`，一旦发生溢出则返回None。
2. `saturating_*`系列函数返回类型是整数，如果溢出，则给出该类型可表示范围的“最大/最小”值。
3. `wrapping_*`系列函数则是直接抛弃已经溢出的最高位，将剩下的部分返回。

Rust 编译器在编译时默认没有溢出检查（可通过编译参数来引入），但在运行时会有 Rust 内置 lint (`#[deny(arithmetic_overflow)]`)来检查，如果有溢出会 Panic。

无符号整数使用时要注意回绕(wrap around)，不同整数类型转换时需注意截断。



## G.TYP.INT.01 对于大整数字面量使用十六进制表示比十进制更好

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [decimal_literal_representation](https://rust-lang.github.io/rust-clippy/master/#decimal_literal_representation) | yes           | no           | restriction | allow |

### 【描述】

【正例】

```rust
let a = `255` 
let b = `65_535`
let c =`4_042_322_160` 
```

【反例】

```rust
let a = `0xFF` 
let b = `0xFFFF` 
let c = `0xF0F0_F0F0
```

## G.TYP.INT.02  数字字面量在使用的时候应该明确标注好类型

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [default_numeric_fallback](https://rust-lang.github.io/rust-clippy/master/#default_numeric_fallback) | yes           | no           | restriction | allow |

### 【描述】

如果数字字面量没有被指定具体类型，那么单靠类型推导，整数类型会被默认绑定为 `i32` 类型，而浮点数则默认绑定为 `f64`类型。这可能导致某些运行时的意外。

【正例】

```rust
let i = 10u32;
let f = 1.23f32;
```

【反例】

```rust
let i = 10; // i32
let f = 1.23; // f64
```

## G.TYP.INT.03  避免将有符号整数和无符号整数之间强制转换

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [cast_sign_loss](https://rust-lang.github.io/rust-clippy/master/#cast_sign_loss) | yes           | no           | pedantic   | allow |

注意：默认情况下该 lint 是` allow`，如果需要检查这种转换，则需要设置为 `warn`或 `deny`。

### 【描述】

当有符号整数被强制转换为无符号整数时，负值会发生回绕，变成更大的正值，这在实际应用时有可能助长缓冲区溢出风险。

【正例】

```rust
let y : i8 = -1;
// Error: 
// the trait `From<i8>` is not implemented for `u128`
// the trait bound `u128: From<i8>` is not satisfied
let z = u128::from(y);
```

【反例】

```rust
let y: i8 = -1;
y as u128; // will return 18446744073709551615
```



