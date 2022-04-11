## P.TYP.01  必要时，应使类型可以表达更明确的语义，而不是只是直接使用原生类型

**【描述】**

在类型中表达语义，可以增加代码的可读性。

**【反例】**

```rust
fn main() {
    // 不符合
    let years = 1942;
}
```

**【正例】**

```rust
// 符合： 语义更明确
struct Years(i64);

fn main() {
    let years = Years(1942);
    let years_as_primitive_1: i64 = years.0; 
    let Years(years_as_primitive_2) = years; 
}
```
