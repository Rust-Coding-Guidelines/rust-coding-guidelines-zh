## G.MEM.BOX.01  一般情况下，不要直接对 `Box<T>` 进行借用

**【级别】** 建议

**【描述】**

通常 `&T` 比 `&Box<T>` 更常用。

**【反例】**

```rust
fn foo(bar: &Box<T>) { ... }
```

**【正例】**

```rust
fn foo(bar: &T) { ... }
```

**【例外】**

```rust
// https://docs.rs/crate/actix-web-security/0.1.0/source/src/authentication/scheme/authentication_provider.rs

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
