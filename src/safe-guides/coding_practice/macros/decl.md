# 声明宏

[声明宏](https://doc.rust-lang.org/reference/macros-by-example.html) 也被叫做 示例宏（macros by example），或者简单地叫做 宏。目前声明宏使用 `macro_rules!`来定义。

声明宏的特点是：它只用作代码替换，而无法进行计算。

## 列表

- [P.MAC.DCL声明宏内的变量作为外部变量使用](./decl/P.MAC.DCL.01.md)
- [P.MAC.DCL.02 在编写多个宏规则时，应该先从匹配粒度最小的开始写](./decl/P.MAC.DCL.02.md)
- [P.MAC.DCL.03 不要在片段分类符跟随它不匹配的符号](./decl/P.MAC.DCL.03.md)
- [P.MAC.DCL.04 匹配规则要精准，不要模糊不清](./decl/P.MAC.DCL.04.md)
- [P.MAC.DCL.05 使用宏替换（substitution）元变量的时候要注意选择合适的片段分类符](./decl/P.MAC.DCL.05.md)
- [P.MAC.DCL.06 当宏需要接收 self 时需要注意](./decl/P.MAC.DCL.06.md)
- [P.MAC.DCL.07 确保在宏定义之后再去调用宏](./decl/P.MAC.DCL.07.md)
- [P.MAC.DCL.08 同一个 crate 内定义的宏相互调用时，需要注意卫生性](./decl/P.MAC.DCL.08.md)