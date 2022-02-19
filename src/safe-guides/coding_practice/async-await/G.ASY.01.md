## G.ASY.01 在 `async` 块/函数中调用 `async` 函数/闭包请不要忘记添加`.await`

**【级别】** 建议

**【描述】**

在此条件下 `.await` 语句通常为必须的。

**【反例】**

```rust
async fn foo() {}

fn bar() {
  let x = async {
    foo()
  };
}
```

**【正例】**

```rust
async fn foo() {}

fn bar() {
  let x = async {
    foo().await
  };
}
```

**【例外】**

用例来源：[fishrock_lambda_runtime](https://docs.rs/crate/fishrock_lambda_runtime/0.3.0-patched.1/source/src/lib.rs#:~:text=clippy%3a%3aasync_yields_async)

```rust
#[allow(clippy::async_yields_async)]
let task = tokio::spawn(async move { handler.call(body, ctx) });

let req = match task.await {
    // ...
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [async_yields_async](https://rust-lang.github.io/rust-clippy/master/#async_yields_async) | yes           | no           | correctness | deny  |
