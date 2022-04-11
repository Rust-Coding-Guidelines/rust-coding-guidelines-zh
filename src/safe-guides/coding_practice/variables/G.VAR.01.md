## G.VAR.01  以解构元组方式定义超过四个变量时不应使用太多无意义变量名

**【级别】** 建议

**【描述】**

在以解构元组的方式定义超过四个变量时，变量名可能是无特别语义的，如用单个字符表示的临时变量。但是不宜使用过多无意义变量名。

**【反例】**

```rust
#![warn(clippy::many_single_char_names)]
// 不符合
let (a, b, c, d, e, f, g) = (...);
```

**【正例】**

元组元素超过三个的，建议使用包含语义的变量命。

```rust
#![warn(clippy::many_single_char_names)]
// 符合
let (width, high, len, shape, color, status) = (...);
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [many_single_char_names](https://rust-lang.github.io/rust-clippy/master/#many_single_char_names) | yes           | no           | pedantic   | allow |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 修改可以绑定的单个字符变量名最大数量。默认为 4
single-char-binding-names-threshold=4
```