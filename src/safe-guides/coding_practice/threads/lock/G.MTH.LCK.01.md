## G.MTH.LCK.01 对布尔或引用并发访问应该使用原子类型而非互斥锁

**【级别】** 建议

**【描述】**

使用原子类型性能更好。但要注意指定合理的内存顺序。

**【反例】**

```rust
// 不符合
let x = Mutex::new(&y);
```

**【正例】**

```rust
// 符合
let x = AtomicBool::new(y);
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [mutex_atomic](https://rust-lang.github.io/rust-clippy/master/#mutex_atomic) | yes           | no           | perf       | warn  |
