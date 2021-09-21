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



## G.CNS.02 不要断言常量布尔类型

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [assertions_on_constants](https://rust-lang.github.io/rust-clippy/master/#assertions_on_constants) | yes| no | Style | warn |

### 【描述】


## G.CNS.03 不要将内部可变性容器声明为常量

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [borrow_interior_mutable_const](https://rust-lang.github.io/rust-clippy/master/#borrow_interior_mutable_const) | yes| no | Style | warn |
| [declare_interior_mutable_const](https://rust-lang.github.io/rust-clippy/master/#declare_interior_mutable_const) | yes| no | Style | warn |



### 【描述】


## G.CNS.04 不要在常量定义中增加显式的 `'static` 生命周期

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [redundant_static_lifetimes](https://rust-lang.github.io/rust-clippy/master/#redundant_static_lifetimes) | yes| no | Style | warn |

### 【描述】

## G.CNS.05  对于函数或方法应尽可能地使用 `const fn`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [missing_const_for_fn](https://rust-lang.github.io/rust-clippy/master/#missing_const_for_fn) | yes| no | Perf | warn |

### 【描述】