## P.MAC.02 实现宏语法的时候，应该尽量贴近 Rust 语法   

**【描述】**

Rust 宏可以让开发者定义自己的DSL，但是，在使用宏的时候，要尽可能贴近Rust的语法。这样可以增强可读性，让其他开发者在使用宏的时候，可以猜测出它的生成的代码。

【正例】

```rust
bitflags! {
    struct S: u32 { /* ... */ }
}

// 也要注意结尾是正确的分号或逗号
bitflags! {
    struct S: u32 {
        const C = 0b000100;
        const D = 0b001000;
    }
}
```

【反例】

```rust
// ...over no keyword...
bitflags! {
    S: u32 { /* ... */ }
}

// ...or some ad-hoc word.
bitflags! {
    flags S: u32 { /* ... */ }
}

// or
bitflags! {
    struct S: u32 {
        const E = 0b010000, // 结尾应该是分号更符合 Rust 语法
        const F = 0b100000,
    }
}
```
