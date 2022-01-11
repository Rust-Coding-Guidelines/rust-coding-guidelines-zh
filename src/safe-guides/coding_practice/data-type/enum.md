# 枚举体

Rust 的枚举是一种带 Tag 的联合体。 一般分为三类：空枚举、无字段（fieldless）枚举和数据承载（data carrying）枚举。

**【示例】**

```rust
enum Empty {}

enum Fieldless {
    A,
    B,
    C = 42, // 可以自定义判别式
}

enum DataCarrying {
    Foo(i32, i32),
    Bar(String)
}
```

Rust 中枚举体用处很多，你甚至可以将其作为一种接口使用。

