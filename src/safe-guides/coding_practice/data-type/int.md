# 整数

Rust 中有目前有 十二种整数类型：`i8/u8`, `i16/u16`, `i32/u32`, `i64/u64`, `i128/u128`, `isize/usize` 。

---

## G.TYP.INT.01  在用整数计算的时候需要考虑整数溢出、回绕和截断的风险

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [integer_arithmetic](https://rust-lang.github.io/rust-clippy/master/#integer_arithmetic) | yes           | no           | restriction | allow |
| [manual_saturating_arithmetic](https://rust-lang.github.io/rust-clippy/master/#manual_saturating_arithmetic) | yes           | no           | style       | allow |

### 【描述】

需要结合场景和业务来考虑如果发生溢出、回绕或截断的时候，是否会引起严重的问题。

比如，对于时间要求精准的系统，如果在计算时间发生整数溢出，或者去计算某个数组的索引等，那可能会发生严重问题。但如果你只是一个简单的计算器，不会被用到具体的业务场合，那溢出也没有关系，因为你只需要在合理的数字范围内计算性能最好。

在 Rust 标准库中，提供 `add`/ `checked_add` / ` saturating_add`/`overflowing_add` / `wrapping_add` 不同系列方法，返回值不同，根据不同的场合选择适合的方法。

1. `check_*`函数返回`Option`，一旦发生溢出则返回None。
2. `saturating_*`系列函数返回类型是整数，如果溢出，则给出该类型可表示范围的“最大/最小”值。
3. `wrapping_*`系列函数则是直接抛弃已经溢出的最高位，将剩下的部分返回。

Rust 编译器在编译时默认没有溢出检查（可通过编译参数来引入），但在运行时会有 Rust 内置 lint (`#[deny(arithmetic_overflow)]`)来检查，如果有溢出会 Panic。

无符号整数使用时要注意回绕(wrap around)，不同整数类型转换时需注意截断。

【正例】

```rust
assert_eq!((-5i32).checked_abs(), Some(5));
assert_eq!(100i32.saturating_add(1), 101);
```

【反例】

```rust
assert_eq!((-5i32).abs(), 5);
assert_eq!(100i32+1, 101);
```



## G.TYP.INT.02 对于大整数字面量使用十六进制表示比十进制更好

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [decimal_literal_representation](https://rust-lang.github.io/rust-clippy/master/#decimal_literal_representation) | yes           | no           | restriction | allow |

### 【描述】

【正例】

```rust
let a = `0xFF`
let b = `0xFFFF`
let c = `0xF0F0_F0F0
```

【反例】

```rust
let a = `255` 
let b = `65_535`
let c =`4_042_322_160` 
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



## G.TYP.INT.04  对负数取模计算的时候不要使用 `%`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [modulo_arithmetic](https://rust-lang.github.io/rust-clippy/master/#modulo_arithmetic) | yes           | no           | restriction | allow |

### 【描述】

Rust 当前的这个 `%`形式是余数运算符，它的行为与`C`或`Java`等语言中相同符号的运算符相同。它也类似于`Python`或`Haskell`等语言中的模（modulo）运算符，只是它对 负数 的行为不同：余数是基于截断除法，而模运算是基于向下取整（floor）除法。

【正例】

```rust
fn main() {
    let a: i32 = -1;
    let b: i32 = 6;
	//  取模是严格低于第二个操作数的自然数（所以是非负数），与第二个操作数的最大倍数相加，也低于或等于第一个操作数，则为第一个操作数。
    //  6的最大倍数低于或等于-1 是 -6（6*-1），模数是5，因为-6+5=-1。
    assert_eq!(a.rem_euclid(b), 5);
}
```

【反例】

```rust
fn main() {
    let a: i32 = -1;
    let b: i32 = 6;
    // 余数运算符只是返回第一个操作数除以第二个操作数的余数。所以 -1/6 给出 0，余数为 -1
    assert_eq!(a % b, -1);
}
```













