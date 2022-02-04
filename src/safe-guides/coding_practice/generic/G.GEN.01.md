
## G.GEN.01 不要在泛型位置上使用内建类型

**【级别】** 建议

**【描述】**

这样做虽然会导致编译错误，但是这种错误会使开发者感到困惑，反而无法找到问题所在。

**【反例】**

这里 `u32` 会被认为是一个类型参数。

```rust
impl<u32> Foo<u32> {
    fn impl_func(&self) -> u32 {
        42
    }
}
```

**【正例】**

```rust
impl<T> Foo<T> {
    fn impl_func(&self) -> T {
        42
    }
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [builtin_type_shadow](https://rust-lang.github.io/rust-clippy/master/#builtin_type_shadow) | yes| no | style | warn |