## G.TYP.INT.03  对负数取模计算的时候不应使用`%`

**【级别】** 建议

**【描述】**

Rust 中的 `%` 符号为余数运算符，它的行为与`C`或`Java`等语言中相同符号的运算符相同。它也类似于`Python`或`Haskell`等语言中的模（modulo）运算符，只是它对负数的行为不同：余数是基于截断除法，而模运算是基于向下取整（floor）除法。

**【反例】**

```rust
fn main() {
    let a: i32 = -1;
    let b: i32 = 6;
    // 余数运算符只是返回第一个操作数除以第二个操作数的余数。所以 -1/6 给出 0，余数为 -1
    assert_eq!(a % b, -1);
}
```

**【正例】**

```rust
fn main() {
    let a: i32 = -1;
    let b: i32 = 6;
	//  取模是严格低于第二个操作数的自然数（所以是非负数），与第二个操作数的最大倍数相加，也低于或等于第一个操作数，则为第一个操作数。
    //  6的最大倍数低于或等于-1 是 -6（6*-1），模数是5，因为-6+5=-1。
    assert_eq!(a.rem_euclid(b), 5);
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [modulo_arithmetic](https://rust-lang.github.io/rust-clippy/master/#modulo_arithmetic) | yes           | no           | restriction | allow |


