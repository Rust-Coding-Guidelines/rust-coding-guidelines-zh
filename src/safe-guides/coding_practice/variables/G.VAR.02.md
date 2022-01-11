## G.VAR.02  使用解构元组方式定义多个变量时不应使用太多单个字符来命名变量

**【级别】** 建议

**【描述】**

在解构元组的方式定义多个变量时，有时候变量命名可能是无特别语义的，比如临时值，可以用简单的单个字符来定义变量名，但是不宜太多。

该 lint 对应 `clippy.toml` 配置项：

```toml
# 修改可以绑定的单个字符变量名最大数量。默认为 4
single-char-binding-names-threshold=4
```

**【反例】**

```rust
let (a, b, c, d, e, f, g) = (...);
```

**【正例】**

超过三个的，就需要起带语义的命名。

```rust
let (width, high, len, shape, color, status) = (...);
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [many_single_char_names](https://rust-lang.github.io/rust-clippy/master/#many_single_char_names) | yes           | no           | pedantic   | allow |

