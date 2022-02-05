# 锁同步

Rust 中多线程并发使用锁来进行线程同步。

## 列表

- [P.MTH.LOK.01 根据场景选择使用互斥锁还是 Channel](./lock/P.MTH.LOK.01.md)
- [P.MTH.LOK.02 多线程下要注意识别锁争用的情况，避免死锁](./lock/P.MTH.LOK.02.md)
- [G.MTH.LOK.01 对布尔或引用并发访问应该使用原子类型而非互斥锁](./lock/G.MTH.LOK.01.md)
- [G.MTH.LOK.02 多线程环境下宜使用 Arc 代替 Rc](./lock/G.MTH.LOK.02.md)
- [G.MTH.LOK.03 建议使用 `Arc<str> / Arc<[T]>` 来代替 `Arc<String> / Arc<Vec<T>>`](./lock/G.MTH.LOK.03.md)
- [G.MTH.LOK.04 尽量避免直接使用标准库 `std::sync` 模块中的同步原语，替换为 `parking_lot`](./lock/G.MTH.LOK.04.md)
- [G.MTH.LOK.05 尽量避免直接使用标准库 `std::sync::mpsc` 模块中的 `channel`，替换为 `crossbeam`](./lock/G.MTH.LOK.05.md)