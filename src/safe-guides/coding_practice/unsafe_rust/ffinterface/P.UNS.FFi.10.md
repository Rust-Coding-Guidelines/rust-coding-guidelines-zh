## P.UNS.FFi.10 当 Rust 函数导出外部函数时，必须从设计上保证被跨线程调用的安全性

**【描述】**

当 Rust 函数被导出为外部函数接口时，要保证其被跨线程调用的安全性。除非调用它的环境是单线程。

**【正例】**

```rust
pub extern "C" fn nic_udrv_suspend() {
	NIC_ENTITY.try_borrow_mut().suspend(); // suspend()需要可变引用
}

// 对外被 C 调用的接口
pub extern "C" fn nic_udrv_buf_recycle(buf_id: usize) {
	NIC_ENTITY.try_borrow().buf_recycle(buf_id); // buf_recycle()内有锁可以避免多线程竞争
}
```