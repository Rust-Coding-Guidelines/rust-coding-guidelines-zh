# 智能指针

智能指针，在 Rust 中参与自动管理堆内容、引用计数、抽象指针语义等功能。一般实现了 `Deref` trait 或 `Drop` trait 的类型都可以看作是一种智能指针。

`Box<T>` 就是一个典型的智能指针，但是因为其在 Rust 中有特殊地位，所以为其单独罗列规则。

Rust 中常见的智能指针包括：

- 自动管理堆内存：`Box<T>`
- 引用计数：`Rc<T> / Arc<T>`
- 内部可变性容器：`Cell<T> / RefCell<T>`

## 列表

- [P.MEM.SPT.01 使用 `RefCell<T>` 时宜使用 `try_borrow/try_borrow_mut` 方法](./smart-ptr/P.MEM.SPT.01.md)