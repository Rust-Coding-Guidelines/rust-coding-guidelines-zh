## P.MEM.SPT.01 使用 `RefCell<T>` 时宜使用 `try_borrow/try_borrow_mut` 方法

**【描述】**

Rust 的 `RefCell<T>` 在运行时会对通过 `borrow/borrow_mut` 方法借用出去的不可变借用和可变借用进行检查。如果发现违反了借用规则的情况，则会 Panic。

所以在一些多线程场景下，开发者可能对细粒度的操作加了锁同步，但是没有对 `RefCell<T>` 进行加锁，此时宜用 `try_borrow/try_borrow_mut` 来代替
`borrow/borrow_mut`，以避免在运行时因为违反借用检查规则而出现 Panic。

**【反例】**

```rust
// 以下两个函数会让 C 函数在多线程下调用
// 运行过程中有一定几率会出现 Panic
pub extern "C" fn nic_udrv_suspend() {
    NIC_ENTITY.borrow_mut().suspend(); // suspend()需要可变引用
}

pub extern "C" fn nic_udrv_buf_recycle(buf_id: usize) {
    NIC_ENTITY.borrow().buf_recycle(buf_id); // buf_recycle()内有锁可以避免多线程竞争
}
```

**【正例】**

```rust
// 以下两个函数会让 C 函数在多线程下调用
// 使用 try_borrow 或 try_borrow_mut 可以避免运行过程中出现 Panic
pub extern "C" fn nic_udrv_suspend() {
    if let Ok(entity) = NIC_ENTITY.try_borrow_mut() {
        entity.suspend(); // suspend()需要可变引用
    }
}

pub extern "C" fn nic_udrv_buf_recycle(buf_id: usize) {
    if let Ok(entity) = NIC_ENTITY.try_borrow() {
        entity.buf_recycle(buf_id); // buf_recycle()内有锁可以避免多线程竞争
    }
}
```
