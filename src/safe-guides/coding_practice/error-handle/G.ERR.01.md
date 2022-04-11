## G.ERR.01  在处理 `Option<T>` 和 `Result<T, E>` 类型时，不要随便使用 `unwrap`

**【级别】** 建议

**【描述】**

当 `Option<T>` 和 `Result<T, E>`类型的值分别是 `None` 或 `Err` 时，直接对其 `unwrap()` 会导致程序恐慌！

**【反例】**

```rust
#![warn(clippy::unwrap_used)]

fn select(opt: Option<String>) {
    opt.unwrap();  // 不符合
}
// OR
fn select(opt: Result<String, ()>) {
    res.unwrap();  // 不符合
}
```

**【正例】**

```rust
#![warn(clippy::unwrap_used)]

fn select(opt: Option<String>) {
    opt.expect("more helpful message");  // 符合：可以用 expect 方法来处理 None 的情况
}
// OR
fn select(opt: Result<String, ()>) {
    res.expect("more helpful message");  // 符合：可以用 expect 方法来处理 Err 的情况
}
```

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [unwrap_used](https://rust-lang.github.io/rust-clippy/master/#unwrap_used) | yes           | no           | restriction | allow |

