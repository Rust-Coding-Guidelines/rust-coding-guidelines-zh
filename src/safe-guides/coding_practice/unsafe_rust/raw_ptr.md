# 裸指针操作

Rust提供了`*const T`（不变）和`*mut T`（可变）两种指针类型。因为这两种指针和C语言中的指针十分相近，所以叫其原生指针（Raw Pointer）。

原生指针具有以下特点：

- 并不保证指向合法的内存。比如很可能是一个空指针。
- 不能像智能指针那样自动清理内存。需要像 C 语言那样手动管理内存。
- 没有生命周期的概念，也就是说，编译器不会对其提供借用检查。
- 不能保证线程安全。

可见，原生指针并不受Safe Rust提供的那一层“安全外衣”保护，所以也被称为“裸指针”。

## 列表

- [P.UNS.PTR.01 不要将裸指针在多线程间共享](./raw_ptr/P.UNS.PTR.01.md)
- [P.UNS.PTR.02 建议使用 `NonNull<T>` 来替代 `*mut T`](./raw_ptr/P.UNS.PTR.02.md)
- [P.UNS.PTR.03 使用指针类型构造泛型结构体时，需要使用 PhantomData<T> 来指定 T上的协变和所有权](./raw_ptr/P.UNS.PTR.03.md)
- [G.UNS.PTR.01 当指针类型被强转为和当前内存对齐不一致的指针类型时，禁止对其解引用](./raw_ptr/G.UNS.PTR.01.md)
- [G.UNS.PTR.02 禁止将不可变指针手工转换为可变指针](./raw_ptr/G.UNS.PTR.02.md)
- [G.UNS.PTR.03 尽量使用 pointer::cast 来代替 使用 as 强转指针](./raw_ptr/G.UNS.PTR.03.md)
