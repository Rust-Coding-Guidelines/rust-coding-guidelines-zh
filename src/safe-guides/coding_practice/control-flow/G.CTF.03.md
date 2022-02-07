## G.CTF.03 如果要通过 `if` 条件表达式来判断是否panic，请优先使用断言

**【级别】** 建议

**【描述】**

略

**【反例】**

```rust
let sad_people: Vec<&str> = vec![];
if !sad_people.is_empty() {
    panic!("there are sad people: {:?}", sad_people);
}
```

**【正例】**

```rust
let sad_people: Vec<&str> = vec![];
assert!(sad_people.is_empty(), "there are sad people: {:?}", sad_people);
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [if_then_panic](https://rust-lang.github.io/rust-clippy/master/#if_then_panic) | yes           | no           | Style   |warn|



