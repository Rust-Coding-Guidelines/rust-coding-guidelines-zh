# 3.17 多线程

Rust 天生可以有效消除数据竞争。

## 列表

- [锁同步](./threads/lock.md)
    - [P.MTH.LOK.01 多线程下要注意识别锁争用的情况，避免死锁](./threads/lock/P.MTH.LOK.01.md)
    - [G.MTH.LOK.01 对布尔或引用并发访问应该使用原子类型而非互斥锁](./threads/lock/G.MTH.LOK.01.md)
    - [G.MTH.LOK.02 建议使用 `Arc<str> / Arc<[T]>` 来代替 `Arc<String> / Arc<Vec<T>>`](./threads/lock/G.MTH.LOK.02.md)
    - [G.MTH.LOK.03 尽量避免直接使用标准库 `std::sync` 模块中的同步原语，替换为 `parking_lot`](./threads/lock/G.MTH.LOK.03.md)
    - [G.MTH.LOK.04 尽量避免直接使用标准库 `std::sync::mpsc` 模块中的 `channel`，替换为 `crossbeam`](./threads/lock/G.MTH.LOK.04.md)
- [无锁](./threads/lock-free.md)
    - [P.MTH.LKF.01 除非必要，否则建议使用同步锁](./threads/lock-free/P.MTH.LKF.01.md)
    - [P.MTH.LKF.02 使用无锁编程时，需要合理选择内存顺序](./threads/lock-free/P.MTH.LKF.02.md)