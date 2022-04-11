## G.ASY.04 避免定义不必要的异步函数

**【级别】** 建议

**【描述】**

如果一个异步函数内部没有任何异步代码，相比一个同步函数，它会产生额外的调用成本。

**【反例】**

```rust
// 不符合
#[warn(clippy::unused_async)]
async fn add(value: i32) -> i32 {
    value + 1 
}
```

**【正例】**

```rust
// 符合
#[warn(clippy::unused_async)]
fn add(value: i32) -> i32 {
    value + 1
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [unused_async](https://rust-lang.github.io/rust-clippy/master/#unused_async) | yes           | no           | pedantic   | allow |
