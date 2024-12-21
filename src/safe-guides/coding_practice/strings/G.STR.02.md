## G.STR.02  在追加字符串时使用`push_str`方法

**【级别】** 建议

**【描述】**

增强代码的可读性

**【反例】**

```rust
#![warn(clippy::string_add_assign, clippy::string_add)]

let mut x = "Hello".to_owned();
x = x + ", World"; // 不符合
```

**【正例】**

```rust
#![warn(clippy::string_add_assign, clippy::string_add)]

let mut x = "Hello".to_owned();

// More readable
x += ", World";
x.push_str(", World"); // 符合
```

**【Lint 检测】**

| lint name                                                                              | Clippy 可检测 | Rustc 可检测 | Lint Group  | 默认level |
| -------------------------------------------------------------------------------------- | ------------- | ------------ | ----------- | --------- |
| [string_add_assign](https://rust-lang.github.io/rust-clippy/master/#string_add_assign) | yes           | no           | pedantic    | allow     |
| [string_add](https://rust-lang.github.io/rust-clippy/master/#string_add)               | yes           | no           | restriction | allow     |



