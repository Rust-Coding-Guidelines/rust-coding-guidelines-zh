## P.FMT.16  不要将派生宏中多个不相关的特质合并为同一行

**【描述】**

不要将派生宏（Derive）中多个特质（trait）合并为同一行，这样可以增加代码可读性，明确语义。

`rustfmt` 配置项 `merge_derives` 用于匹配此格式，其默认值是让派生宏中多个特质在同一行，所以需要修改其默认值。

说明： `rustfmt` 并不会识别哪些特质相关，所以需要开发者手工指定好。

**【反例】**

当使用默认设置  `merge_derives = true` 时，不符合。

```rust
// 不符合：不相关的特质放到一行
#[derive(Eq, PartialEq, Debug, Copy, Clone)]
pub enum Foo {}
```

**【正例】**

修改默认设置 `merge_derives = false`，符合。

```rust
// 符合
#[derive(Eq, PartialEq)]
#[derive(Debug)]
#[derive(Copy, Clone)]
pub enum Foo {}
```

**【rustfmt 配置】**

| 对应选项                                                     | 可选值                     | 是否 stable | 说明                             |
| ------------------------------------------------------------ | -------------------------- | ----------- | -------------------------------- |
| [`merge_derives`](https://rust-lang.github.io/rustfmt/?#merge_derives) | true（默认） false（推荐） | Yes         | 是否将多个 Derive 宏合并为同一行 |
