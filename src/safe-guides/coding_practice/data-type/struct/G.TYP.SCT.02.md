## G.TYP.SCT.02  当结构体中有超过三个布尔类型的字段，宜将其独立为新的枚举类

**【级别】** 建议

**【描述】**

这样有助于提升 代码可读性和 API 。

**【反例】**

```rust
struct S {
    is_pending: bool,
    is_processing: bool,
    is_finished: bool,
}
```

**【正例】**

```rust
enum S {
    Pending,
    Processing,
    Finished,
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [struct_excessive_bools](https://rust-lang.github.io/rust-clippy/master/#struct_excessive_bools) | yes           | no           | pedantic   | allow |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 用于配置函数可以拥有的 bool 类型参数最大数量，默认为 3。
max-struct-bools=3 
```

