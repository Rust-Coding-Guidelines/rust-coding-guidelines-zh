## G.OTH.02  使用标准库中对应的方法计算秒级、毫秒级、微秒级的时间

**【级别】** 建议

**【描述】**

略。

**【反例】**

```rust
# use std::time::Duration;
let dur = Duration::new(5, 0);

// Bad
let _micros = dur.subsec_nanos() / 1_000;      // 用纳秒计算微妙
let _millis = dur.subsec_nanos() / 1_000_000;  // 用纳秒计算毫秒
```

**【正例】**

```rust
# use std::time::Duration;
let dur = Duration::new(5, 0);

// Good
let _micros = dur.subsec_micros(); // 通过标准库函数得到微秒
let _millis = dur.subsec_millis(); // 通过标准库函数得到毫秒
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [duration_subsec](https://rust-lang.github.io/rust-clippy/master/#duration_subsec) | yes           | no           | complexity | warn  |
