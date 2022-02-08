# 3.10 泛型

Rust 中的泛型允许开发人员编写更加简洁、更少重复的代码。但泛型可能会引起编译文件大小膨胀，酌情使用。

## 列表

- [P.GEN.01 用泛型来抽象公共语义](./generic/P.GEN.01.md)
- [P.GEN.02 不要随便使用 impl Trait 语法替代泛型限定](./generic/P.GEN.02.md)
- [P.GEN.03 不要使用太多泛型参数和 trait 限定，否则会增长编译时间](./generic/P.GEN.03.md)
- [P.GEN.04 为泛型类型实现方法时，impl 中声明的泛型类型参数一定要被用到](./generic/P.GEN.04.md)
- [P.GEN.05 定义泛型函数时，如果该函数实现用到来自 trait 定义的相关行为，需要为泛型指定相关 trait 的限定](./generic/P.GEN.05.md)
- [G.GEN.01 不要在泛型位置上使用内建类型](./generic/G.GEN.01.md)
- [G.GEN.02 使用 Rust 标准库中某些方法，要注意避免使用其泛型默认实现，而应该使用具体类型的实现](./generic/G.GEN.02.md)