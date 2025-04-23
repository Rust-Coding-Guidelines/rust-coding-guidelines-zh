# 3.19 Unsafe Rust

Unsafe Rust 是 Safe Rust 的超集，意味着在 Unsafe Rust 中也会有 Safe Rust 的安全检查。但是 Unsafe Rust 中下面五件事是Safe Rust 的检查鞭长莫及的地方：

1. 解引用裸指针
2. 调用 `unsafe`函数（C函数，编译器内部函数或原始分配器）
3. 实现 `unsafe` trait
4. 可变静态变量
5. 访问  `union` 的字段

使用 Unsafe Rust 的时候，需要遵守一定的规范，这样可以避免未定义行为的发生。

关于 Unsafe Rust 下的一些专用术语可以查看 [Unsafe 代码术语指南](./unsafe_rust/glossary.md) 。

**Unsafe Rust 的语义：这是编译器无法保证安全的地方，需要程序员来保证安全。** 

[Unsafe 代码术语指南](./unsafe_rust/glossary.md)