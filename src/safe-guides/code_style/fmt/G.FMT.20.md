## G.FMT.20  不要将多个不相关的 派生(Derive) 宏合并为同一行

**【级别】** 建议

**【描述】**

不要将多个 Derive 宏合并为同一行，可以增加代码可读性，明确语义。
rustfmt 不会识别Derive 宏是否相关，这是开发者自己编写的。

**【反例】**

用默认设置  `merge_derives = true`

```rust
#[derive(Eq, PartialEq, Debug, Copy, Clone)]
pub enum Foo {}
```

**【正例】**

修改默认设置 `merge_derives = false`

```rust
#[derive(Eq, PartialEq)]
#[derive(Debug)]
#[derive(Copy, Clone)]
pub enum Foo {}
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项                                                     | 可选值                     | 是否 stable | 说明                             |
| ------------------------------------------------------------ | -------------------------- | ----------- | -------------------------------- |
| [`merge_derives`](https://rust-lang.github.io/rustfmt/?#merge_derives) | true（默认） false（推荐） | Yes         | 是否将多个 Derive 宏合并为同一行 |
