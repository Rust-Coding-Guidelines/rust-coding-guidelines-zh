# 异步编程


---

## P.ASY.01 

【描述】


---

## G.ASY.01 在 `async` 块/函数中调用 `async` 函数/闭包请不要忘记添加`.await`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [async_yields_async](https://rust-lang.github.io/rust-clippy/master/#async_yields_async) | yes| no | correctness | deny |

也有例外情况。

### 【描述】


【正例】

```rust
async fn foo() {}

fn bar() {
  let x = async {
    foo().await
  };
}
```

【反例】

```rust
async fn foo() {}

fn bar() {
  let x = async {
    foo()
  };
}
```

【例外】

```rust
// https://docs.rs/crate/fishrock_lambda_runtime/0.3.0-patched.1/source/src/lib.rs#:~:text=clippy%3a%3aasync_yields_async

#[allow(clippy::async_yields_async)]
let task = tokio::spawn(async move { handler.call(body, ctx) });

let req = match task.await {
    // ...
}

```


## G.ASY.02 在 跨`await` 调用中持有同步锁需要进行处理

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [await_holding_lock](https://rust-lang.github.io/rust-clippy/master/#await_holding_lock) | yes| no | pedantic | allow |


### 【描述】


## G.ASY.03 在 跨`await` 调用持有`RefCell`的引用需要进行处理

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [await_holding_refcell_ref](https://rust-lang.github.io/rust-clippy/master/#await_holding_refcell_ref) | yes| no | pedantic | allow |


### 【描述】