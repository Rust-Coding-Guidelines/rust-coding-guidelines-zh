## P.NAM.07 避免使用语言内置保留字、关键字、内置类型和`trait`等特殊名称

**【描述】**

命名必须要避免使用语言内置的保留字、关键字、内置类型和`trait`等特殊名称。 具体可以参考[The Rust Reference-Keywords](https://doc.rust-lang.org/stable/reference/keywords.html)。

**【反例】**

```rust
// 不符合：Rust 内置了 Sized trait 
type Sized = u16; 

fn main() {
    // 不符合：try 为保留关键字
    // 如果必须要用，使用`r#`前缀可以使用它，但要尽力避免
    let r#try = 1;
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