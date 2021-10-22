# 锁同步

Rust 中多线程并发使用锁来进行线程同步。

---

## P.MTH.lock.01  首选  [`parking_lot`](https://crates.io/crates/parking_lot) 中定义的 同步原语，而非标准库 `std::sync` 模块

**【描述】**

标准库中 `std::sync` 模块中实现的锁同步原语，存在一些问题，比如需要使用 `Box<T>` 将操作系统锁原语维持在同一个内存位置，这点浪费内存。而   [`parking_lot`](https://crates.io/crates/parking_lot)  的实现则更加轻量和正确，性能也更好，比如 `parking_lot` 的 `Mutex` 和  `Rwlock` 都支持 最终公平性，在不失性能的基础上保证公平。 目前官方正在推动 parking_lot 进入标准库中。在使用 parking_lot 时注意和标准库的区别。

parking_lot 也提供了一些有用的 feature，比如 死锁检测（deadlock detection），在使用  Mutex 的时候，可以打开这个特性，可以在编译期发现死锁，没准可以节省你很多时间。



