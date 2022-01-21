## G.FMT.18  extern 外部函数需要指定 ABI

**【级别】** 建议

**【描述】**

当使用 `extern` 指定外部函数时，建议显式指定 `C-ABI`。`extern` 不指定的话默认就是 `C-ABI`，但是 Rust 语言显式指定是一种约定俗成。如果是 `Rust-ABI`则不会省略。

**【反例】**

```rust
// 省略 ABI 指定，则默认是 C-ABI
extern {
    pub static lorem: c_int;
}

// 非 C-ABI 是无法省略的
extern "Rust" {
    type MyType;
    fn f(&self) -> usize;
}
```

**【正例】**

```rust
extern "C" {
    pub static lorem: c_int;
}

extern "Rust" {
    type MyType;
    fn f(&self) -> usize;
}
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`force_explicit_abi`](https://rust-lang.github.io/rustfmt/?#force_explicit_abi) | true（默认） | Yes|  extern 外部函数总是要指定 ABI |
