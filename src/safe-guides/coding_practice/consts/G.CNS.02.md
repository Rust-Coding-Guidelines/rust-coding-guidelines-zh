## G.CNS.02 不应断言常量布尔类型

**【级别】** 建议

**【描述】**

此类语句会被编译器优化掉。最好直接使用 `panic!` 或 `unreachable!`代替。

**【反例】**

```rust
// 不符合
const B: bool = false;
assert!(B);
assert!(true);
```

**【正例】**

```rust
// 符合
panic!（"something"）;
```

**【例外】**

该示例需要维护一个常量的不变性，确保它在未来修改时不会被无意中破坏。类似于 [static_assertions](https://docs.rs/static_assertions/1.1.0/static_assertions/) 的作用。

```rust
#![allow(clippy::assertions_on_constants)]
const MIN_OVERFLOW: usize = 8192;
const MAX_START: usize = 2048;
const MAX_END: usize = 2048;
const MAX_PRINTED: usize = MAX_START + MAX_END;
assert!(MAX_PRINTED < MIN_OVERFLOW);
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [assertions_on_constants](https://rust-lang.github.io/rust-clippy/master/#assertions_on_constants) | yes| no | Style | warn |

