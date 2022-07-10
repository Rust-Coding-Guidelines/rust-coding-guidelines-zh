## G.MOD.05 不要在私有模块中设置其内部类型或函数方法为 `pub(crate)`

**【级别】** 建议

**【描述】**

如果在私有模块中设置 `pub(crate)` 可能会让使用者产生误解。建议用 `pub` 代替。

**【反例】**

```rust
mod internal {
    // 不符合
    pub(crate) fn internal_fn() { }
}
```

**【正例】**

```rust
// 符合
mod internal {
    // 此函数在模块外部不可见，可以使用 pub 或 继续保持私有
    pub fn internal_fn() { }
}
```


**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [redundant_pub_crate](https://rust-lang.github.io/rust-clippy/master/#redundant_pub_crate) | yes           | no           | nursery | allow |


