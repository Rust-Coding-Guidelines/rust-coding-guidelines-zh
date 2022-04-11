## P.CMT.02  注释应该有宽度限制

**【描述】**

每行注释的宽度不能过长，需要设置一定的宽度，不超过120，有助于提升可读性。

`rustfmt`中通过`comment_width`配合 `wrap_comments` 配置项，可将超过宽度限制的注释自动分割为多行。

注意：`rustfmt`的 `use_small_heuristics`配置项并不包括`comment_width`。

**【反例】**

```rust
// 不符合
// Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
```

**【正例】**

当 `comment_width=80` 且 `wrap_comments=true`时。

注意：这里 `wrap_comments`并未使用默认值，需要配置为 true。

```rust
// 符合
// Lorem ipsum dolor sit amet, consectetur adipiscing elit,
// sed do eiusmod tempor incididunt ut labore et dolore
// magna aliqua. Ut enim ad minim veniam, quis nostrud
// exercitation ullamco laboris nisi ut aliquip ex ea
// commodo consequat.
```

**【rustfmt 配置】**

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- |
| [`comment_width`](https://rust-lang.github.io/rustfmt/?#comment_width) | 80（默认） | No|  指定一行注释允许的最大宽度 |
| [`wrap_comments`](https://rust-lang.github.io/rustfmt/?#wrap_comments) | false（默认），true（建议） | No| 运行多行注释按最大宽度自动换成多行注释 |