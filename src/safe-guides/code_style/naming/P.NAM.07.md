## P.NAM.07 避免使用语言内置保留字、关键字、内置类型和`trait`等特殊名称

**【描述】**

命名必须要避免使用语言内置的保留字、关键字、内置类型和`trait`等特殊名称。 具体可以参考[The Rust Reference-Keywords](https://doc.rust-lang.org/stable/reference/keywords.html)。

**【反例】**

```rust
// 不符合：Rust 内置了 Sized trait 
type Sized = u16; 

fn main() {
    // 不符合：try 为保留关键字
    let try = 1;
}
```

**【正例】**

```rust
// 符合
type Size = u16; 

fn main() {
    // 符合
    let tried = 1;
}
```

**【例外】**

在一些特定场合，比如对接遗留数据库中的字段和Rust关键字冲突：

```rust

struct SomeTable{
    // 使用 `r#`+type 来解决这种问题
    r#type: String
}

```

或者当序列化为 json 或 proto 时，存在成员为关键字，则可以通过相关库提供的功能来使用：

```rust
pub struct UserRepr {
    // ...
    #[serde(rename="self")]
    pub self_: Option<String>,
    // ...
}
```