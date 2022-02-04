# 常量

在 Rust 中，常量有两种用途：

- 编译时常量（Compile-time constants）
- 编译时求值 （CTEF, compile-time evaluable functions）

常量命名风格指南请看 [编码风格-命名](../code_style/naming.md)

## 列表

- [G.CNS.01 对于科学计算中涉及浮点数近似值的常量宜使用预定义常量](./consts/G.CNS.01.md)
- [G.CNS.02 不应断言常量布尔类型](./consts/G.CNS.02.md)
- [G.CNS.03 不宜将量大的数据结构定义为常量](./consts/G.CNS.03.md)
- [G.CNS.04 不应将内部可变性容器声明为常量](./consts/G.CNS.04.md)
- [G.CNS.05 不应在常量定义中增加显式的 `'static` 生命周期](./consts/G.CNS.05.md)
- [G.CNS.06 对于适用 `const fn` 的函数或方法宜尽可能地使用 `const fn`](./consts/G.CNS.06.md)