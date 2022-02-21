## G.TYP.ENM.02 不应自行创建空枚举

**【级别】** 建议

**【描述】**

在 Rust 中 只有 `never` 类型（`!`）才是唯一合法表达 无法被实例化类型 的类型。但目前 `never` 类型还未稳定，只能在 Nightly 下使用。


**【反例】**

```rust
enum Test {}
```

**【正例】**

所以，如果想在稳定版 Rust 中使用，建议使用[`std::convert::Infallible`](https://doc.rust-lang.org/std/convert/enum.Infallible.html#) 。 `Infallible` 枚举是一个合法的空枚举，常用于错误处理中，表示永远不可能出现的错误。但是目前也可以用于在稳定版中替代  `never`   类型。

```rust
// 未来 never 类型稳定的话，将会把 Infallible 设置为 never 类型的别名
pub type Infallible = !;
```

**【例外】**

因为 [`std::convert::Infallible`](https://doc.rust-lang.org/std/convert/enum.Infallible.html#) 默认实现了很多 trait，如果不想依赖其他 trait ，那么可以用 空枚举。

```rust
pub enum NoUserError {}

impl Display for NoUserError {
    fn fmt(&self, _formatter: &mut fmt::Formatter) -> fmt::Result {
        match *self {}
    }
}

```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group   | level |
| ------------------------------------------------------------ | ------------- | ------------ | ------------ | ----- |
| [empty_enum](https://rust-lang.github.io/rust-clippy/master/#empty_enum) | yes           | no           | **pedantic** | allow |



