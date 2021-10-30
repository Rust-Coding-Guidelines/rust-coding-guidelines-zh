# 常量

在 Rust 中，常量有两种用途：

- 编译时常量（Compile-time constants）
- 编译时求值 （CTEF, compile-time evaluable functions）

常量命名风格指南请看 [编码风格-命名](../code_style/naming.md)

---

## P.CNS.01 常量必须都要持有明确语义以便让人更好地理解

### 【描述】

## P.CNS.02 注意根据常量和静态变量的本质区别来选择何时使用常量或静态变量

### 【描述】

常量 vs 静态变量：

1. 常量会内联到使用它的地方。
2. 静态变量不会内联，它是全局的，且有一个引用地址。

有些场合要根据常量和静态变量的本质区别来选择合适的类型。

比如，当你想创建一个很大的常量数组时，应该考虑将其换成静态变量。因为常量会到处内联。

---


## G.CNS.01 对于科学计算中涉及浮点数近视值的常量要尽量使用预定义常量

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [approx_constant](https://rust-lang.github.io/rust-clippy/master/#approx_constant) | yes| no | Correctness | deny |

该 Lint 默认为 `deny`，但在某些场景下，可以设置为`allow`.

### 【描述】

【正例】

```rust
let x = std::f32::consts::PI;
let y = std::f64::consts::FRAC_1_PI;
```

【反例】

```rust
let x = 3.14;
let y = 1_f64 / x;
```





## G.CNS.02 不要断言常量布尔类型

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [assertions_on_constants](https://rust-lang.github.io/rust-clippy/master/#assertions_on_constants) | yes| no | Style | warn |

### 【描述】

因为有可能被编译器优化掉。

【反例】

```rust
#![allow(clippy::assertions_on_constants)]
const MIN_OVERFLOW: usize = 8192;
const MAX_START: usize = 2048;
const MAX_END: usize = 2048;
const MAX_PRINTED: usize = MAX_START + MAX_END;
assert!(MAX_PRINTED < MIN_OVERFLOW);
```






## G.CNS.03 不要将内部可变性容器声明为常量

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [borrow_interior_mutable_const](https://rust-lang.github.io/rust-clippy/master/#borrow_interior_mutable_const) | yes| no | Style | warn |
| [declare_interior_mutable_const](https://rust-lang.github.io/rust-clippy/master/#declare_interior_mutable_const) | yes| no | Style | warn |

### 【描述】

【正例】

```rust
use std::sync::atomic::{AtomicUsize, Ordering::SeqCst};
const CONST_ATOM: AtomicUsize = AtomicUsize::new(12);

// Good.
static STATIC_ATOM: AtomicUsize = CONST_ATOM;
STATIC_ATOM.store(9, SeqCst);
assert_eq!(STATIC_ATOM.load(SeqCst), 9); // use a `static` item to refer to the same instance
```



【反例】

```rust
use std::sync::atomic::{AtomicUsize, Ordering::SeqCst};
const CONST_ATOM: AtomicUsize = AtomicUsize::new(12);

// Bad.
CONST_ATOM.store(6, SeqCst); // the content of the atomic is unchanged
assert_eq!(CONST_ATOM.load(SeqCst), 12); // because the CONST_ATOM in these lines are distinct

```






## G.CNS.04 不要在常量定义中增加显式的 `'static` 生命周期

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [redundant_static_lifetimes](https://rust-lang.github.io/rust-clippy/master/#redundant_static_lifetimes) | yes| no | Style | warn |

### 【描述】

没必要加。

【正例】

```rust
const FOO: &[(&str, &str, fn(&Bar) -> bool)] = &[...]
 static FOO: &[(&str, &str, fn(&Bar) -> bool)] = &[...]
```

【反例】

```rust
const FOO: &'static [(&'static str, &'static str, fn(&Bar) -> bool)] =
&[...]
static FOO: &'static [(&'static str, &'static str, fn(&Bar) -> bool)] =
&[...]
```





## G.CNS.05  对于函数或方法应尽可能地使用 `const fn`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [missing_const_for_fn](https://rust-lang.github.io/rust-clippy/master/#missing_const_for_fn) | yes| no | Perf | warn |

### 【描述】

【正例】

```rust
const fn new() -> Self {
    Self { random_number: 42 }
}
```

【反例】

```rust
fn new() -> Self {
    Self { random_number: 42 }
}
```

