
## G.UNS.01  不要随便为 带有 `unsafe`命名的 类型或方法创建别名

**【级别：建议】**

建议按此规范执行。

**【描述】**

 Rust 里  `unsafe` 字样用于提醒开发者在编写代码的时候注意保证安全。如果修改别名，隐藏了这种提醒，不利于展示这种信息。

不利于开发者去保证安全。

**【反例】**

```rust
use std::cell::{UnsafeCell as TotallySafeCell};

extern crate crossbeam;
use crossbeam::{spawn_unsafe as spawn};
```

**【正例】**

```rust
use std::cell::{UnsafeCell  };

extern crate crossbeam;
use crossbeam::{spawn_unsafe  };
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [unsafe_removed_from_name](https://rust-lang.github.io/rust-clippy/master/#unsafe_removed_from_name) | yes           | no           | style      | warn  |
