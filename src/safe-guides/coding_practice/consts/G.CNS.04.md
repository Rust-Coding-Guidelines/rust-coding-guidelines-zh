## G.CNS.04 不应在常量定义中增加显式的 `'static` 生命周期

**【级别】** 要求

**【描述】**

在常量和静态变量声明时已经默认含有隐式的`'static`生命周期，所以不需要额外增加显式`'static`。

**【反例】**

```rust
// 不符合
const FOO: &'static [(&'static str, &'static str, fn(&Bar) -> bool)] =
&[...]
static FOO: &'static [(&'static str, &'static str, fn(&Bar) -> bool)] =
&[...]
```

**【正例】**

```rust
// 符合
const FOO: &[(&str, &str, fn(&Bar) -> bool)] = &[...]
static FOO: &[(&str, &str, fn(&Bar) -> bool)] = &[...]
```

**【Lint 检测】**

| lint name                                                                                                | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| -------------------------------------------------------------------------------------------------------- | ------------- | ------------ | ---------- | ----- |
| [redundant_static_lifetimes](https://rust-lang.github.io/rust-clippy/master/#redundant_static_lifetimes) | yes           | no           | Style      | warn  |

