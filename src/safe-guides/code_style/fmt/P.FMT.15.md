## P.FMT.15  解构元组的时候允许使用`..`来指代剩余元素

**【描述】**

默认选项是 false，表示不允许 解构元组的时候使用`..`来指代剩余元素

**【反例】**

这种情况下，rustfmt不会自动更改代码，会保留原来的写法。

```rust
fn main() {
    let (lorem, ipsum, _, _) = (1, 2, 3, 4);
    let (lorem, ipsum, ..) = (1, 2, 3, 4);
    let (lorem, _,ipsum, _, _) = (1, 2, 3, 4, 5);
}
```

**【正例】**

设置 `condense_wildcard_suffixes = true` 时，会强行更改代码为下面形式。

```rust
fn main() {
    let (lorem, ipsum, ..) = (1, 2, 3, 4);
    let (lorem, _,ipsum, ..) = (1, 2, 3, 4, 5);
}
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项                                                     | 可选值                      | 是否 stable | 说明                                         |
| ------------------------------------------------------------ | --------------------------- | ----------- | -------------------------------------------- |
| [`condense_wildcard_suffixes`](https://rust-lang.github.io/rustfmt/?#condense_wildcard_suffixes) | false（默认） true （推荐） | No          | 解构元组的时候是否允许使用`..`来指代剩余元素 |
