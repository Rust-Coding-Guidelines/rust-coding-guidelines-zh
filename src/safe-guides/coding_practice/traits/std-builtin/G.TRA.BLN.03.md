## G.TRA.BLN.03   能使用派生宏（Derive）自动实现`Default`特质就不要用手工实现 

**【级别】** 建议

**【描述】**

手工实现 Default，代码不精炼。 

**【反例】**

```rust
struct Foo {
    bar: bool
}
// 不符合
impl std::default::Default for Foo {
    fn default() -> Self {
        Self {
            bar: false
        }
    }
}
```

**【正例】**

```rust
// 符合
#[derive(Default)]
struct Foo {
    bar: bool
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group     | level |
| ------------------------------------------------------------ | ------------- | ------------ | -------------- | ----- |
| [derivable_impls](https://rust-lang.github.io/rust-clippy/master/#derivable_impls) | yes           | no           | **complexity** | warn  |

该lint不能用于检测泛型参数类型的 Default 手工实现。
