# 在 Rust 生态中被拒绝的一些默认开启的lint

来源：[https://github.com/dtolnay/noisy-clippy](https://github.com/dtolnay/noisy-clippy)

以下按字母顺序排列。

## `absurd_extreme_comparisons`

[https://rust-lang.github.io/rust-clippy/master/index.html#absurd_extreme_comparisons](https://rust-lang.github.io/rust-clippy/master/index.html#absurd_extreme_comparisons)

【描述】

默认为 `Deny`，但在实际应用中，多被设置为 `allow`。


## `blacklisted_name`

[https://rust-lang.github.io/rust-clippy/master/index.html#blacklisted_name](https://rust-lang.github.io/rust-clippy/master/index.html#blacklisted_name)

【描述】

该 lint 不允许代码中出现 「内置黑名单」中定义的命名，比如 `foo`、`baz`。

默认为 `Warn`，但在实际应用中，可能被设置为`allow`，因为在某些样板代码、文档或测试代码中可能需要使用 `foo`。


## `blanket_clippy_restriction_lints`

[https://rust-lang.github.io/rust-clippy/master/index.html#blanket_clippy_restriction_lints](https://rust-lang.github.io/rust-clippy/master/index.html#blanket_clippy_restriction_lints)

【描述】

用于检查针对整个 `clippy::restriction` 类别的警告/拒绝/禁止属性。Restriction lint 有时与其他 lint 形成对比，甚至与惯用的 Rust 背道而驰。 这些 lint 应仅在逐个 lint 的基础上启用并仔细考虑。

默认为 `suspicious/warn`，但实际有些项目中会将其设置为 `allow`。