## G.STR.02   在追加字符串时使用`push_str`方法可读性更强

**【级别】** 建议

**【描述】**

略

**【反例】**

```rust
let mut x = "Hello".to_owned();
x = x + ", World";
```

**【正例】**

```rust
let mut x = "Hello".to_owned();

// More readable
x += ", World";
x.push_str(", World");
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [string_add_assign](https://rust-lang.github.io/rust-clippy/master/#string_add_assign) | yes           | no           | pedantic    | allow |
| [string_add](https://rust-lang.github.io/rust-clippy/master/#string_add) | yes           | no           | restriction | allow |



