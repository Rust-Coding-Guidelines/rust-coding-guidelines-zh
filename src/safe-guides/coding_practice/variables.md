# 3.3 本地变量

这里所说的变量单指局部变量而不包括全局变量。 默认情况下，Rust 会强制初始化所有变量的值，以防止使用未初始化的内存。

## 列表

- [P.VAR.01 一般情况下避免先声明可变变量再赋值](./variables/P.VAR.01.md)
- [P.VAR.02 利用变量遮蔽功能保证变量安全使用](./variables/P.VAR.02.md)
- [G.VAR.01 以解构元组方式定义超过四个变量时不应使用太多无意义变量名](./variables/G.VAR.01.md)
- [G.VAR.02 不应使用非 ASCII 字符作为标识符](./variables/G.VAR.02.md)
- [G.VAR.03 变量遮蔽功能应当合理使用](./variables/G.VAR.03.md)
- [G.VAR.04 避免因局部变量过大而导致的大量栈分配](./variables/G.VAR.04.md)