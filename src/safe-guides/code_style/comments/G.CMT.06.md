## G.CMT.06  如果公开的API在某些情况下会发生Panic，则相应文档中需增加 `# Panic` 注释

**【级别】** 建议

**【描述】**

在公开（pub）函数文档中，建议增加 `# Panic` 注释来解释该函数在什么条件下会 Panic，便于使用者进行预处理。

说明： 该规则通过 cargo clippy 来检测。默认不会警告。

**【反例】**

```rust
pub fn divide_by(x: i32, y: i32) -> i32 {
    if y == 0 {
        panic!("Cannot divide by 0")
    } else {
        x / y
    }
}
```

**【正例】**

```rust
/// # Panics
///
/// Will panic if y is 0
pub fn divide_by(x: i32, y: i32) -> i32 {
    if y == 0 {
        panic!("Cannot divide by 0")
    } else {
        x / y
    }
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认 level |
| ------ | ---- | --------- | ------ | ------ | 
| [missing_panics_doc ](https://rust-lang.github.io/rust-clippy/master/index.html#missing_panics_doc ) | yes| no | Style | allow | 