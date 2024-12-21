## G.CAR.03 Feature 命名应该避免否定式或多余的前后缀

**【级别】** 建议

**【描述】**

Feature 命名应该避免出现 `no-` 或 `not-` 之类的否定前缀，或诸如 `use-`，`with-` 前缀或 `-support`后缀。Feature 的目的是正向的，可选的特性，使用否定式命名和它的目的背道而驰。

**【反例】**

```toml
[features]
default = ["no-abc", "with-def", "ghi-support"]
no-abc = []         # 不符合：命名否定式
with-def = []       # 不符合：多余前缀
ghi-support = []    # 不符合：多余后缀
```

**【正例】**

```toml
# 符合
[features]
default = ["abc", "def", "ghi"]
abc = []
def = []
ghi = []
```

**【Lint 检测】**

| lint name                                                                                          | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认level |
| -------------------------------------------------------------------------------------------------- | ------------- | ------------ | ---------- | --------- |
| [negative_feature_names](https://rust-lang.github.io/rust-clippy/master/#negative_feature_names)   | yes           | no           | cargo      | allow     |
| [redundant_feature_names](https://rust-lang.github.io/rust-clippy/master/#redundant_feature_names) | yes           | no           | cargo      | allow     |
