## P.FMT.14  extern 外部函数需要显式指定 C-ABI

**【描述】**

当使用 `extern` 指定外部函数时，建议显式指定 `C-ABI`。

虽然 `extern` 不指定的话默认就是 `C-ABI`，但是 Rust 语言显式指定是一种约定俗成。


**【反例】**

```rust
// 不符合：不要省略 C-ABI 指定
extern {
    pub static lorem: c_int;
}
```

**【正例】**

```rust
// 符合
extern "C" {
    pub static lorem: c_int;
}

extern "Rust" {
    type MyType;
    fn f(&self) -> usize;
}
```

**【rustfmt 配置】**

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`force_explicit_abi`](https://rust-lang.github.io/rustfmt/?#force_explicit_abi) | true（默认） | Yes|  extern 外部函数总是要指定 ABI |
