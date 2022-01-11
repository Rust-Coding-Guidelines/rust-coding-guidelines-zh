## G.TRA.Buitin.03   能使用`derive` 自动实现`Default`  trait 就不要用手工实现 

**【级别】** 建议

**【描述】**

手工实现 Default，代码不精炼。 

**【反例】**

```rust
struct Foo {
    bar: bool
}

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
