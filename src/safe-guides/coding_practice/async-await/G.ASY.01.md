## G.ASY.01 在 `async` 块或函数中调用 `async` 函数或闭包请不要忘记添加`.await`

**【级别】** 建议

**【描述】**

在此条件下 `.await` 语句通常为必须的。

**【反例】**

```rust
async fn foo() {}

fn bar() {
  let x = async {
    foo() // 不符合
  };
}
```

**【正例】**

```rust
async fn foo() {}

fn bar() {
  let x = async {
    foo().await // 符合
  };
}
```


**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [async_yields_async](https://rust-lang.github.io/rust-clippy/master/#async_yields_async) | yes           | no           | correctness | deny  |
