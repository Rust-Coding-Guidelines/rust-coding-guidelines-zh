## P.UNS.UNI.01  除了与 C 交互，尽量不要使用 Union

**【描述】**

Rust 支持 Union 就是为了调用 C 接口。如果不是 FFi ，就避免使用 Union。

一般情况下请使用 枚举 或 结构体代替。

使用 `Copy` 类型的值和 `ManuallyDrop` 来初始化 Union 的变体，不需要使用 Unsafe 块。

**【反例】**

```rust
union MyUnion {
    f1: u32,
    f2: f32,
}
```

**【正例】**

```rust
#[repr(C)]
union MyUnion {
    f1: u32,
    f2: f32,
}
```
