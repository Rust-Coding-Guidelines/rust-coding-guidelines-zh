## G.CAR.04 Cargo.toml 中依赖包版本不应使用通配符

**【级别】** 要求

**【描述】**

依赖的包必须指定具体的语义版本。关于语义版本说明参见：[The Cargo Book: SemVer Compatibility](https://doc.rust-lang.org/cargo/reference/semver.html)。

> 使用 Clippy 需要设置 `#[warn(clippy::wildcard_dependencies)]`。

**【反例】**

```toml
[dependencies]
regex = "*" # 不符合：避免项目依赖因为上游更新而自动更新
```

**【正例】**

```toml
[dependencies]
regex = "1.5" # 符合
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [wildcard_dependencies](https://rust-lang.github.io/rust-clippy/master/#wildcard_dependencies) | yes           | no           | cargo      | allow |
