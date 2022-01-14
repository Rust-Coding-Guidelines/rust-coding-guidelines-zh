## G.MEM.BOX.01  一般情况下，不应直接对 `Box<T>` 进行借用

**【级别】** 建议

**【描述】**

借用 `Box<T>` 等同于直接借用 `T`，而 `&T` 要比 `&Box<T>` 更常用。

**【反例】**

```rust
fn foo(bar: &Box<T>) { ... }
```

**【正例】**

```rust
fn foo(bar: &T) { ... }
```

**【例外】**

用例来源：[actix-web-security](https://github.com/cschaible/actix-web-security/blob/6e3a7716a1391ea880da85dfa4631dce3aaafd18/src/authentication/scheme/authentication_provider.rs#L12)

```rust
#[async_trait]
pub trait AuthenticationProvider: AuthenticationProviderClone {
    #[allow(clippy::borrowed_box)]
    async fn authenticate(
        &self,
        authentication: &Box<dyn Authentication>,
    ) -> Result<Box<dyn UserDetails>, AuthenticationError>;
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [borrowed_box](https://rust-lang.github.io/rust-clippy/master/#borrowed_box) | yes| no | complexity | warn |
