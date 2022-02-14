## P.GEN.03   不要使用太多泛型参数和 trait 限定，否则会增长编译时间

**【描述】**

为泛型函数添加详细的 trait 限定，可以在一定程度上增强用户使用体验，但使用过多的泛型参数和 trait 限定会显著地增长编译时间。

**【反例】**

此写法比下面的写法编译时间要多十倍。

```rust
    // From: https://github.com/tokio-rs/axum/pull/198
    fn handle_error<ReqBody, ResBody, F, Res, E>(
        self,
        f: F,
    ) -> HandleError<Self, F, ReqBody, HandleErrorFromRouter>
    where
        Self: Service<Request<ReqBody>, Response = Response<ResBody>>,
        F: FnOnce(Self::Error) -> Result<Res, E>,
        Res: IntoResponse,
        ResBody: http_body::Body<Data = Bytes> + Send + Sync + 'static,
        ResBody::Error: Into<BoxError> + Send + Sync + 'static,
    {
        HandleError::new(self, f)
    }
```

**【正例】**

来自于 Web 框架 Axum 的代码：

```rust
    // From: https://github.com/tokio-rs/axum/pull/198
    fn handle_error<ReqBody, F>(
        self,
        f: F,
    ) -> HandleError<Self, F, ReqBody, HandleErrorFromRouter> {
        HandleError::new(self, f)
    }   
```
