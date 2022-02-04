# Box 类型

Rust 中分配堆内存必须要使用的类型，类型签名为 `Box<T>`。

## 列表

- [G.MEM.BOX.01 一般情况下，不应直接对 `Box<T>` 进行借用](./box/G.MEM.BOX.01.md)
- [G.MEM.BOX.02 一般情况下，不应直接对已经在堆上分配内存的类型进行 Box 装箱](./box/G.MEM.BOX.02.md)
- [G.MEM.BOX.03 一般情况下，不应直接对栈分配类型进行 Box 装箱](./box/G.MEM.BOX.03.md)