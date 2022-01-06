## G.MTH.lock.02   多线程环境下要使用 `Arc` 代替 `Rc`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [rc_mutex](https://rust-lang.github.io/rust-clippy/master/#rc_mutex) | yes           | no           | restriction | allow |

### 【描述】

`Rc` 是专门用于单线程的，多线程下应该用  `Arc` 。

【正例】

```rust
use std::rc::Rc;
use std::sync::Arc;
use std::cell::RefCell
fn foo(interned: Rc<RefCell<i32>>) { ... }
// or
fn foo(interned: Arc<Mutex<i32>>) { ... }
```

【反例】

```rust
use std::rc::Rc;
use std::sync::Mutex;
fn foo(interned: Rc<Mutex<i32>>) { ... }
```
