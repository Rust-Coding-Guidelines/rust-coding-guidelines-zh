## G.GEN.02   使用 Rust 标准库中某些方法，要注意避免使用其泛型默认实现，而应该使用具体类型的实现

**【级别】** 建议

**【描述】**

Rust 标准库内部某些类型使用了 泛型特化（未稳定特性），比如 `ToString` trait。

该 trait 有一个[泛型默认实现](https://doc.rust-lang.org/stable/src/alloc/string.rs.html#2369)， 并且一些具体类型也实现了它，比如 `char`/ `str` /  `u8`/ `i8` 等。

在实际代码中，应该选择去调用具体类型实现的 `to_string()` 方法，而非调用泛型的默认实现。 

**这一规则要求开发者对 Rust 标准库的一些方法实现有一定了解。** 

**【反例】**

```rust
#![warn(clippy::inefficient_to_string)]

// 不符合
// 闭包参数中， s 为 `&&str` 类型
//  `&&str` 就会去调用泛型的默认实现
["foo", "bar"].iter().map(|s| s.to_string() );
```

**【正例】**

```rust
#![warn(clippy::inefficient_to_string)]

// 符合
// 闭包参数中， s 为 `&&str` 类型，使用 `|&s|` 对参数模式匹配后，闭包体内 `s` 就变成了 `&str` 类型
// 经过这样的转换，直接调用 `&str`的 `to_string()` 方法，而如果是 `&&str` 就会去调用泛型的默认实现。 
["foo", "bar"].iter().map(|&s| s.to_string() );
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [inefficient_to_string](https://rust-lang.github.io/rust-clippy/master/#inefficient_to_string) | yes           | no           | pedantic   | allow |