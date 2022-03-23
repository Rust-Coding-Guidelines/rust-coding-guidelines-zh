## G.TYP.INT.01  在用整数计算的时候需要考虑整数溢出、回绕和截断的风险

**【级别】** 建议

**【描述】**

使用整数计算时需要结合场景和业务来考虑如果发生溢出、回绕或截断的时候，是否会引起严重的问题。

比如，对于时间要求精准的系统，如果在计算时间发生整数溢出，或者去计算某个数组的索引等，那可能会发生严重问题。但如果你只是一个简单的计算器，不会被用到具体的业务场合，那溢出也没有关系，因为你只需要在合理的数字范围内计算性能最好。

在 Rust 标准库中，提供 `add`/ `checked_add` / ` saturating_add`/`overflowing_add` / `wrapping_add` 不同系列方法，返回值不同，根据不同的场合选择适合的方法。

1. `check_*`函数返回`Option`，一旦发生溢出则返回None。
2. `saturating_*`系列函数返回类型是整数，如果溢出，则给出该类型可表示范围的“最大/最小”值。
3. `wrapping_*`系列函数则是直接抛弃已经溢出的最高位，将剩下的部分返回。

Rust 编译器在编译时默认没有溢出检查（可通过编译参数来引入），但在运行时会有 Rust 内置 lint (`#[deny(arithmetic_overflow)]`)来检查，如果有溢出会 Panic。

无符号整数使用时要注意回绕(wrap around)，不同整数类型转换时需注意截断。

**【反例】**

```rust
assert_eq!((-5i32).abs(), 5);
assert_eq!(100i32+1, 101);

fn test_integer_overflow() {
    // debug 与 release 编译都会有溢出检查
    let mut a: u8 = 255 + 1;
    
    // debug模式，运行panic；release模式，x = 0
    let mut x: u8 = 255;
    x += 1;
    println!("x={}", x);
}
```

**【正例】**

```rust
assert_eq!((-5i32).checked_abs(), Some(5));
assert_eq!(100i32.saturating_add(1), 101);

fn add_num(a: u8) -> u8 { a.wrapping_add(255) }

fn test_integer_overflow() {
    // 对于字面量或常量表达式，debug 与 release 编译模式都会有溢出检查
    let mut a: u8 = 255 + 1;

    // debug模式，运行会Panic
    // release模式，x 会等于 0
    let mut x: u8 = 255;
    x = add_num(x);
    println!("x={}", x);
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [integer_arithmetic](https://rust-lang.github.io/rust-clippy/master/#integer_arithmetic) | yes           | no           | restriction | allow |
| [manual_saturating_arithmetic](https://rust-lang.github.io/rust-clippy/master/#manual_saturating_arithmetic) | yes           | no           | style       | warn |




