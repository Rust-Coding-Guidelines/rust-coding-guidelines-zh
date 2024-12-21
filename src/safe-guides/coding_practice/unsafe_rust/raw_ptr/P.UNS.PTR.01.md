## P.UNS.PTR.01  不要将裸指针在多线程间共享

**【描述】**

裸指针在 Rust 中不是线程安全的，将裸指针在多线程传递编译器也会编译出错。如果需要在多线程间共享裸指针，则考虑使用 `NewType` 模式来包装它。

**【正例】**

```rust
struct MyBox(*mut u8);

unsafe impl Send for MyBox {}
unsafe impl Sync for MyBox {}
```