## P.UNS.FFi.04 如果一个函数正在跨越 FFi 边界，那么需要处理 Panic

**【描述】**

如果让 Panic 在跨越 FFi 边界时发生，可能会产生未定义行为。

处理 Panic 可以使用 `catch_unwind`，但是它只对实现了 `UnwindSafe` trait 的类型起作用。另外一种方法就是避免 Panic，而返回错误码。

**【正例】**

```rust
use std::panic::catch_unwind;

#[no_mangle]
pub extern fn oh_no() -> i32 {
    let result = catch_unwind(|| {
        panic!("Oops!"); // 这里会发生 Panic，需要处理
    });
    match result {
        Ok(_) => 0,
        Err(_) => 1,
    }
}

fn main() {}
```
