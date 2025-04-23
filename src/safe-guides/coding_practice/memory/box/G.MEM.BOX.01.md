## G.MEM.BOX.01  一般情况下，不应直接对 `Box<T>` 进行借用

**【级别】** 建议

**【描述】**

借用 `Box<T>` 等同于直接借用 `T`，而 `&T` 要比 `&Box<T>` 更常用。

**【反例】**

```rust
// 不符合
fn foo(bar: &Box<T>) { ... }
```

**【正例】**

```rust
// 符合
fn foo(bar: &T) { ... }
```


**【Lint 检测】**

| lint name                                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认level |
| ---------------------------------------------------------------------------- | ------------- | ------------ | ---------- | --------- |
| [borrowed_box](https://rust-lang.github.io/rust-clippy/master/#borrowed_box) | yes           | no           | complexity | warn      |
