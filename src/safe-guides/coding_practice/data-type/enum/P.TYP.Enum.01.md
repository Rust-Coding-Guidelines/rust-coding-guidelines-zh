## P.TYP.Enum.01 需要取出 Enum 值的时候宜使用 `std::mem::take/swap/replace`

**【描述】**

需要取出 Enum 中值的时候，可能会遇到所有权的限制，此时可以使用 `std::men::take` 获取当前的值，而将默认值替换原值，这样可以避免所有权的限制。

但是 `std::men::take` 只适合实现 `Default` 的类型，这样就有默认实现可以替换了。

如果没有实现 `Default` 的类型，可以使用 `std::men::swap` 或 `std::mem::replace` 用给定的值来替换原值。

**【正例】**

```rust
use std::mem;

enum MultiVariateEnum {
    A { name: String },
    B { name: String },
    C,
    D
}

fn swizzle(e: &mut MultiVariateEnum) {
    use MultiVariateEnum::*;
    *e = match e {
        // Ownership rules do not allow taking `name` by value, but we cannot
        // take the value out of a mutable reference, unless we replace it:
        A { name } => B { name: mem::take(name) },
        B { name } => A { name: mem::take(name) },
        C => D,
        D => C
    }
}
```

---


