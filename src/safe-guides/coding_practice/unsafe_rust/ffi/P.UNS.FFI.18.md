# P.UNS.FFI.18 避免将 trait 对象传递给 C 接口

## 【描述】

Rust 中的多态性主要由 trait 来提供。但是在 FFi 时，将 Rust trait 对象传递给 C 接口，并不能保证 FFi 安全。因为 Rust trait 对象没有稳定的 ABI，所以我们不能通过 `Box<dyn Trait>` 值传递越过 FFI 边界。

所以，最好的方式是不要在 FFi 时通过传递 trait对象来使用多态性。

> 如果必须要在 FFi 中使用多态性，有以下几种方式：
> 1. 使用枚举。像 C 传递一个指向枚举的指针。
> 2. 使用 [`thin_trait_object`](https://github.com/kotauskas/thin_trait_object) 模式，是 FFi 安全的。
