## P.ERR.04   当程序中需要处理错误时，应该使用 `Result<T, E>` 和 `?` 操作符 

**【描述】**

当需要处理错误时，为了保证 程序的健壮性，应该尽可能处理错误。

**【反例】**

在实现原型类项目的时候，可以“快、糙、猛”地使用 `expect`  。但是要进生产环境，需要合理地处理错误。

```rust
let res: Result<usize, ()> = Ok(1);
res.expect("one"); // 如果有 Err， expect会 Panic ！

```

**【正例】**

```rust
let res: Result<usize, ()> = Ok(1);
res?;   // Ok::<(), ()>(())
```