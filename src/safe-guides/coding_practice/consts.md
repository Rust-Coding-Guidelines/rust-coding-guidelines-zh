# 常量

在 Rust 中，常量有两种用途：

- 编译时常量（Compile-time constants）
- 编译时求值 （CTEF, compile-time evaluable functions）

常量命名风格指南请看 [编码风格-命名](../code_style/naming.md)

---
<!-- toc -->
---


## G.CNS.01 对于科学计算中涉及浮点数近似值的常量宜使用预定义常量

**【级别】** 建议

**【描述】**

Rust标准库中已经提供了一些特殊常量的定义，其精确度通常会比开发者自行定义的高，所以若考虑数值精确度时则宜使用标准库已定义的特殊常量。

**【反例】**

```rust
let x = 3.14;
let y = 1_f64 / x;
```

**【正例】**

```rust
let x = std::f32::consts::PI;
let y = std::f64::consts::FRAC_1_PI;
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [approx_constant](https://rust-lang.github.io/rust-clippy/master/#approx_constant) | yes| no | Correctness | deny |

该 Lint 默认为 `deny`，但在某些场景下，可以设置为`allow`.


## G.CNS.02 不应断言常量布尔类型

**【级别】** 必须

**【描述】**

此类语句会被编译器优化掉。最好直接使用 `panic!` 或 `unreachable!`代替。

**【反例】**

```rust
const B: bool = false;
assert!(B);
assert!(true);
```

**【正例】**

```rust
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


## G.CNS.03 不宜将量大的数据结构定义为常量

**【级别】** 建议

**【描述】**

因为[常量会到处内联](https://doc.rust-lang.org/reference/items/constant-items.html#constant-items)，即复制到各个使用到它的地方。而静态变量不会内联，它是全局的且有一个引用地址。
所以当要创建一个很大的常量数组时，应该考虑将其换成静态变量以提高程序运行效率。（详情可见：[const-vs-static](https://rust-lang.github.io/rfcs/0246-const-vs-static.html#motivation)）

**【反例】**

```rust
fn main() {
    const MONTHS: [&str; 12] = ["January", "Feburary", "March", "April",
                                "May", "June", "July", "August",
                                "September", "October", "November", "December"];
}
```

**【正例】**

```rust
fn main() {
    static MONTHS: [&str; 12] = ["January", "Feburary", "March", "April",
                                "May", "June", "July", "August",
                                "September", "October", "November", "December"];
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| _ | no           | no           | _   | yes |

**【定制化参考】**

这条规则如果需要定制Lint，则需要找出每个定义的常量再判断其空间占用，或可直接排除基础类型以外的数据类型。


## G.CNS.04 不应将内部可变性容器声明为常量

**【级别】** 必须

**【描述】**

由于常量会到处内联的特性。
若将一个内容可变容器声明为常量，那么在引用它的时候同样会新建一个实例，这样会破坏内容可变容器的使用目的，
所以需要将它的值存储为静态（static）或者直接将其定义为静态。

**【反例】**

```rust
use std::sync::atomic::{AtomicUsize, Ordering::SeqCst};
const CONST_ATOM: AtomicUsize = AtomicUsize::new(12);

// Bad.
CONST_ATOM.store(6, SeqCst); // 此处相当于新建了一个atomic实例，所以原容器内容并未改变
assert_eq!(CONST_ATOM.load(SeqCst), 12); // 仍为12，因为这两行的CONST_ATOM为不同实例

```

**【正例】**

```rust
use std::sync::atomic::{AtomicUsize, Ordering::SeqCst};
const CONST_ATOM: AtomicUsize = AtomicUsize::new(12);

// Good.
static STATIC_ATOM: AtomicUsize = CONST_ATOM;
STATIC_ATOM.store(9, SeqCst);
assert_eq!(STATIC_ATOM.load(SeqCst), 9); // 使用`static`, 故上下文的STATIC_ATOM皆指向同一个实例

// 或直接声明为static
static ANOTHER_STATIC_ATOM: AtomicUsize = AtomicUsize::new(15);
ANOTHER_STATIC_ATOM.store(9, SeqCst);
assert_eq!(ANOTHER_STATIC_ATOM.load(SeqCst), 9);
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [borrow_interior_mutable_const](https://rust-lang.github.io/rust-clippy/master/#borrow_interior_mutable_const) | yes| no | Style | warn |
| [declare_interior_mutable_const](https://rust-lang.github.io/rust-clippy/master/#declare_interior_mutable_const) | yes| no | Style | warn |


## G.CNS.05 不应在常量定义中增加显式的 `'static` 生命周期

**【级别】** 必须

**【描述】**

在常量和静态变量声明时已经默认含有隐式的`'static`生命周期，所以不需要额外增加显式`'static`声明周期。

**【反例】**

```rust
const FOO: &'static [(&'static str, &'static str, fn(&Bar) -> bool)] =
&[...]
static FOO: &'static [(&'static str, &'static str, fn(&Bar) -> bool)] =
&[...]
```

**【正例】**

```rust
const FOO: &[(&str, &str, fn(&Bar) -> bool)] = &[...]
 static FOO: &[(&str, &str, fn(&Bar) -> bool)] = &[...]
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [redundant_static_lifetimes](https://rust-lang.github.io/rust-clippy/master/#redundant_static_lifetimes) | yes| no | Style | warn |


## G.CNS.06  对于适用 `const fn` 的函数或方法宜尽可能地使用 `const fn`

**【级别】** 建议

**【描述】**

函数或方法缺失`const`关键词时无法被指派给常量。
但是要注意不是所有函数都能使用`const fn`，因为相比一般函数或方法，`const fn`在使用时会有限制，一些功能将无法在`const fn`内使用，例如迭代器。

**【反例】**

```rust
fn foo() -> usize {
    10
}

let bar: usize = foo();   // OK
const BAZ: usize = foo(); // ERROR
```

**【正例】**

```rust
const fn foo() -> usize {
    10
}

let bar: usize = foo():   // OK
const BAZ: usize = foo(); // OK
```

**【例外】**

```rust
const fn foo() -> bool {
    for _i in 0..5 {}      // ERROR, 因为for loop默认不能用在const fn内（需要注明#![feature(const_for)]）
    false
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [missing_const_for_fn](https://rust-lang.github.io/rust-clippy/master/#missing_const_for_fn) | yes| no | Perf | warn |

