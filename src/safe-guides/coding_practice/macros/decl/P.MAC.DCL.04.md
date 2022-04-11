## P.MAC.DCL.04 匹配规则要精准，不要模糊不清

**【描述】**

匹配规则必须精准，因为宏解析器并不会去执行代码，它无法匹配模糊不清的规则。

**【反例】**

宏解析器无法确定第一次匹配的应该是多少个 `ident`。

```rust
macro_rules! ambiguity {
    ($($i:ident)* $i2:ident) => { };
}

// error:
//    local ambiguity: multiple parsing options: built-in NTs ident ('i') or ident ('i2').
fn main() { ambiguity!(an_identifier); }
```

**【正例】**

```rust
macro_rules! ambiguity {
    ($i2:ident $($i:ident)* ) => { };
}

// ok
fn main() { ambiguity!(an_identifier  an_identifier2); }
```
