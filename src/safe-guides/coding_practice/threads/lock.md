# 锁同步

Rust 中多线程并发使用锁来进行线程同步。

## 列表

- [P.MTH.LCK.01 多线程下要注意识别锁争用的情况，避免死锁](./lock/P.MTH.LCK.01.md)
- [G.MTH.LCK.01 对布尔或引用并发访问应该使用原子类型而非互斥锁](./lock/G.MTH.LCK.01.md)
- [G.MTH.LCK.02 建议使用 `Arc<str> / Arc<[T]>` 来代替 `Arc<String> / Arc<Vec<T>>`](./lock/G.MTH.LCK.02.md)
- [G.MTH.LCK.03 尽量避免直接使用标准库 `std::sync` 模块中的同步原语，替换为 `parking_lot`](./lock/G.MTH.LCK.03.md)
- [G.MTH.LCK.04 尽量避免直接使用标准库 `std::sync::mpsc` 模块中的 `channel`，替换为 `crossbeam`](./lock/G.MTH.LCK.04.md)