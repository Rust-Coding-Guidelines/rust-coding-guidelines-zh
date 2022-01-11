## P.TYP.03   必要时，应该使得类型可以表达更明确的语义，而不是只是直接使用原生类型

**【描述】**

这样可以增加代码的可读性。

**【反例】**

```rust
fn main() {
    let years = 1942;
}
```

**【正例】**

```rust
struct Years(i64);

fn main() {
    let years = Years(1942);
    let years_as_primitive_1: i64 = years.0; // Tuple
    let Years(years_as_primitive_2) = years; // Destructuring
}
```

---
