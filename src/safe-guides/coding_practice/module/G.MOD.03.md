
## G.MOD.03 导入模块不要随便使用 通配符`*` 

**【级别】** 建议

**【描述】**

使用通配符导入会污染命名空间，比如导入相同命名的函数或类型。

**【反例】**

```rust
#![warn(clippy::wildcard_imports)]
use crate2::*; // Has a function named foo
foo(); // Calls crate1::foo
```

**【正例】**

```rust
#![warn(clippy::wildcard_imports)]
use crate1::foo; // Imports a function named foo
foo(); // Calls crate1::foo
```

**【例外】**

```rust
use prelude::*;

#[test]
use super::*
```

**【Lint 检测】**

| lint name                                                                            | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认level |
| ------------------------------------------------------------------------------------ | ------------- | ------------ | ---------- | --------- |
| [wildcard_imports](https://rust-lang.github.io/rust-clippy/master/#wildcard_imports) | yes           | no           | pedantic   | allow     |

该 lint 可以通过 clippy 配置项 `warn-on-all-wildcard-imports = false` 来配置，用于是否禁用 `prelude`/ `super` (测试模块中) 使用通配符导入， 默认是 `false`。
