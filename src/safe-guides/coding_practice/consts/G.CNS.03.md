## G.CNS.03 不应将内部可变性容器声明为常量

**【级别】** 要求

**【描述】**

由于常量有内联的特性。若将一个内容可变容器声明为常量，那么在引用它的时候同样会新建一个实例，这样会破坏内容可变容器的使用目的，
所以需要将它的值存储为静态（static）或者直接将其定义为静态。

**【反例】**

```rust
use std::sync::atomic::{AtomicUsize, Ordering::SeqCst};
const CONST_ATOM: AtomicUsize = AtomicUsize::new(12);

// 不符合
CONST_ATOM.store(6, SeqCst); // 此处相当于新建了一个atomic实例，所以原容器内容并未改变
assert_eq!(CONST_ATOM.load(SeqCst), 12); // 仍为12，因为这两行的CONST_ATOM为不同实例

```

**【正例】**

```rust
use std::sync::atomic::{AtomicUsize, Ordering::SeqCst};
const CONST_ATOM: AtomicUsize = AtomicUsize::new(12);

// 符合
static STATIC_ATOM: AtomicUsize = CONST_ATOM;
STATIC_ATOM.store(9, SeqCst);
assert_eq!(STATIC_ATOM.load(SeqCst), 9); // 使用`static`, 故上下文的STATIC_ATOM皆指向同一个实例

// 符合： 或直接声明为static
static ANOTHER_STATIC_ATOM: AtomicUsize = AtomicUsize::new(15);
ANOTHER_STATIC_ATOM.store(9, SeqCst);
assert_eq!(ANOTHER_STATIC_ATOM.load(SeqCst), 9);
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [borrow_interior_mutable_const](https://rust-lang.github.io/rust-clippy/master/#borrow_interior_mutable_const) | yes| no | Style | warn |
| [declare_interior_mutable_const](https://rust-lang.github.io/rust-clippy/master/#declare_interior_mutable_const) | yes| no | Style | warn |
