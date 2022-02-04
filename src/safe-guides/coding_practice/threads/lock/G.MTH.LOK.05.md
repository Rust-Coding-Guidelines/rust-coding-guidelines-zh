## G.MTH.LOK.05 尽量避免直接使用标准库 `std::sync::mpsc` 模块中的 `channel`，替换为 [`crossbeam`](https://github.com/crossbeam-rs/crossbeam)

**【级别】** 建议

**【描述】**

尽量避免使用 `std::sync::mpsc::channel`，建议使用 [`crossbeam`](https://github.com/crossbeam-rs/crossbeam)

**【反例】**

例子来源于 [`std::sync::mpsc` 文档](https://doc.rust-lang.org/std/sync/mpsc/)

```rust
use std::thread;
use std::sync::mpsc::channel;

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
use crossbeam_channel::unbounded;

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

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | 是否可定制 |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| _ | no           | no           | _ | yes |

**【定制化参考】**
这条规则如果需要定制 Lint，则可以扫描对 `std::sync::mpsc::channel` 的使用，推荐优先选择 crate `crossbeam`。
