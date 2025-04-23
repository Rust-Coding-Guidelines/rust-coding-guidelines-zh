## G.MTH.LCK.04 尽量使用[`crossbeam`](https://github.com/crossbeam-rs/crossbeam)模块的 `channel`，而不是`std::sync::mpsc::channel` 

**【级别】** 建议

**【描述】**

尽量避免使用 `std::sync::mpsc::channel`，建议使用 [`crossbeam`](https://github.com/crossbeam-rs/crossbeam)

**【反例】**

例子来源于 [`std::sync::mpsc` 文档](https://doc.rust-lang.org/std/sync/mpsc/)

```rust
use std::thread;
use std::sync::mpsc::channel; // 不符合

let (tx, rx) = channel();

for i in 0..10 {
    let tx = tx.clone();
    thread::spawn(move|| {
        tx.send(i).unwrap();
    });
}

for _ in 0..10 {
    let j = rx.recv().unwrap();
    assert!(0 <= j && j < 10);
}
```

**【正例】**

```rust
use crossbeam_channel::unbounded; // 符合

let (tx, rx) = unbounded();

for i in 0..10 {
    let tx = tx.clone();
    thread::spawn(move|| {
        tx.send(i).unwrap();
    });
}

for _ in 0..10 {
    let j = rx.recv().unwrap();
    assert!(0 <= j && j < 10);
}
```
