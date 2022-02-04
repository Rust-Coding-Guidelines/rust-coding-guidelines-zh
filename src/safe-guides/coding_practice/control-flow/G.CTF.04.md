## G.CTF.04 `if`条件表达式分支中如果包含了`else if`分支也应该包含`else`分支

**【级别】** 建议

**【描述】**

略

**【反例】**

```rust
# fn a() {}
# fn b() {}
# let x: i32 = 1;
if x.is_positive() {
    a();
} else if x.is_negative() {
    b();
}
```

**【正例】**

```rust
# fn a() {}
# fn b() {}
# let x: i32 = 1;
if x.is_positive() {
    a();
} else if x.is_negative() {
    b();
} else {
    // We don't care about zero.
}
```



【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group      | level |
| ------------------------------------------------------------ | ------------- | ------------ | --------------- | ----- |
| [else_if_without_else](https://rust-lang.github.io/rust-clippy/master/#else_if_without_else) | yes           | no           | **restriction** | allow |



