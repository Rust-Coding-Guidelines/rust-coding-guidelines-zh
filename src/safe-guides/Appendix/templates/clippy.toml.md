# Clippy 模板

有些 Clippy 的 Lint，依赖于一些配置项，如果不想要默认值，可以在 `clippy.toml` 中进行设置。

```toml
# for `disallowed_method`:
# https://rust-lang.github.io/rust-clippy/master/index.html#disallowed_method
disallowed-methods = []
```

