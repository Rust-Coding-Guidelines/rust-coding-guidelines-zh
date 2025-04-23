## G.MTH.LCK.03 尽量使用[`parking_lot`](https://crates.io/crates/parking_lot)，而不是标准包 `std::sync`

**【级别】** 建议

**【描述】**

尽量避免对标准库 `std::sync` 模块中锁同步原语的使用，建议使用 [`parking_lot`](https://crates.io/crates/parking_lot) 的实现。

**【反例】**

来源于 [std标准库文档](https://doc.rust-lang.org/std/sync/struct.Mutex.html)

```rust
// 不符合
use std::sync::{Arc, Mutex};
use std::thread;
use std::sync::mpsc::channel;

const N: usize = 10;

let data = Arc::new(Mutex::new(0));

let (tx, rx) = channel();
for _ in 0..N {
    let (data, tx) = (Arc::clone(&data), tx.clone());
    thread::spawn(move || {      
        let mut data = data.lock().unwrap();
        *data += 1;
        if *data == N {
            tx.send(()).unwrap();
        }
    });
}

rx.recv().unwrap();
```

**【正例】**

例子来源于 [parking_lot 文档](https://docs.rs/parking_lot/0.11.2/parking_lot/type.Mutex.html)

相比`std::sync::Mutex`，使用 `parking_lot::Mutex` 能实现'无中毒'，锁在 panic 时正常释放，更少的空间占用等优势。

```rust
// 符合
use parking_lot::Mutex;
use std::sync::{Arc, mpsc::channel};
use std::thread;

const N: usize = 10;

let data = Arc::new(Mutex::new(0));

let (tx, rx) = channel();
for _ in 0..10 {
    let (data, tx) = (Arc::clone(&data), tx.clone());
    thread::spawn(move || {
        let mut data = data.lock();
        *data += 1;
        if *data == N {
            tx.send(()).unwrap();
        }
    });
}

rx.recv().unwrap();
```