## G.TRA.BLN.05   在使用`#[derive(Ord)]` 的时候，避免再手工实现 `PartialOrd`

**【级别】** 要求

**【描述】**

跟实现 Hash 和 Eq 的要求类似，对于实现` Ord` 的类型来说，必须要满足下面一个等式：

```text
k1.cmp(&k2) == k1.partial_cmp(&k2).unwrap()
```

所以要求与  `PartialOrd`   的实现必须保持一致，并确保`max`、`min`和`clamp`与`cmp`一致。

通过`#[derive(Ord)]` 并手动实现`PartialOrd`，很容易意外地使cmp和partial_cmp不一致。

但也有例外。

**【反例】**

```rust
#[derive(Ord, PartialEq, Eq)]
struct Foo;
// 不符合
impl PartialOrd for Foo {
    ...
}
```

**【正例】**

```rust
// 符合
#[derive(Ord, PartialOrd, PartialEq, Eq)]
struct Foo;

// 符合
#[derive(PartialEq, Eq)]
struct Foo;

impl PartialOrd for Foo {
    fn partial_cmp(&self, other: &Foo) -> Option<Ordering> {
       Some(self.cmp(other))
    }
}

impl Ord for Foo {
    ...
}

```

**【例外】**

使用 `#[derive(PartialOrd)]` 自动实现 `PartialOrd`，然后再手工实现 `Ord`的时候在内部调用自动实现的`partial_cmp` ，应该是满足 `k1.cmp(&k2) == k1.partial_cmp(&k2).unwrap()` 了。

```rust
// From: https://docs.rs/crate/adventjson/0.1.1/source/src/lib.rs
#[derive(Clone, Debug, PartialEq, PartialOrd)]
pub enum JsonObject
{
    /// An array of objects (e.g.: \[1,2,3\])
    Array(Vec<Self>),
    /// Key-value pairs (e.g.: {\"first\": 10, \"other\": 15})
    Obj(Vec<(String, Self)>),
    /// A number (e.g.: -0.08333)
    Number(f64),
    /// A string (e.g.: \"Test: \\\"\")
    JsonStr(String),
    /// A boolean (e.g. true)
    Bool(bool),
    /// The null-value
    Null,
}

/// Save because no not-number values are allowed in json
impl Eq for JsonObject {}

/// Save because no not-number values are allowed in json
#[allow(clippy::derive_ord_xor_partial_ord)]
impl Ord for JsonObject
{
    fn cmp(&self, other: &Self) -> Ordering
    {
        self.partial_cmp(other).unwrap()
    }
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [derive_ord_xor_partial_ord](https://rust-lang.github.io/rust-clippy/master/#derive_ord_xor_partial_ord) | yes           | no           | correctness | deny  |