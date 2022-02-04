# 变量

这里所说变量是指局部变量。默认情况下，Rust 会强制初始化所有值，以防止使用未初始化的内存。

变量命名风格指南请看 [编码风格-命名](../code_style/naming.md)

## 列表

- [P.VAR.01 非必要请避免像 C 语言那样先声明可变变量再赋值](./variables/P.VAR.01.md)
- [P.VAR.02 禁止将局部变量的引用返回函数外](./variables/P.VAR.02.md)
- [P.VAR.03 利用变量遮蔽功能保证变量安全使用](./variables/P.VAR.03.md)
- [G.VAR.01 交换两个变量的值应使用 swap 而非赋值](./variables/G.VAR.01.md)
- [G.VAR.02 使用解构元组方式定义多个变量时不应使用太多单个字符来命名变量](./variables/G.VAR.02.md)
- [G.VAR.03 通常不应使用非 ASCII 字符作为标识符](./variables/G.VAR.03.md)
- [G.VAR.04 在某些场合下不宜使用变量遮蔽功能](./variables/G.VAR.04.md)
- [G.VAR.05 避免局部变量导致的大量栈分配](./variables/G.VAR.05.md)