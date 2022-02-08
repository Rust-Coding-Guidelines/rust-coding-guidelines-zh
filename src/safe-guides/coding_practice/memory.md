# 3.13 内存管理

## 列表

- [生命周期](./memory/lifetime.md)
    - [P.MEM.LFT.01 生命周期参数命名尽量有意义且简洁](./memory/lifetime/P.MEM.LFT.01.md)
    - [P.MEM.LFT.02 通常需要显式地标注生命周期，而非利用编译器推断](./memory/lifetime/P.MEM.LFT.02.md)
- [智能指针](./memory/smart-ptr.md)
    - [P.MEM.SPT.01 使用 `RefCell<T>` 时宜使用 `try_borrow/try_borrow_mut` 方法](./memory/smart-ptr/P.MEM.SPT.01.md)
- [Box 类型](./memory/box.md)
    - [G.MEM.BOX.01 一般情况下，不应直接对 `Box<T>` 进行借用](./memory/box/G.MEM.BOX.01.md)
    - [G.MEM.BOX.02 一般情况下，不应直接对已经在堆上分配内存的类型进行 Box 装箱](./memory/box/G.MEM.BOX.02.md)
    - [G.MEM.BOX.03 一般情况下，不应直接对栈分配类型进行 Box 装箱](./memory/box/G.MEM.BOX.03.md)
- [Drop 析构](./memory/drop.md)
    - [G.MEM.DRP.01 要注意防范内存泄漏](./memory/drop/G.MEM.DRP.01.md)
