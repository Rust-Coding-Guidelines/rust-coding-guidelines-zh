## G.TYP.CHR.02 字符串方法中如果需要单个字符的值作为参数，宜使用字符而非字符串

**【级别】** 建议

**【描述】**

大部分情况下，使用字符比用字符串性能更好。

**【反例】**

```rust
let s = "yxz";
s.split("x");
```

**【正例】**

```rust
let s = "yxz";
s.split('x');
```


**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [single_char_pattern](https://rust-lang.github.io/rust-clippy/master/#single_char_pattern) | yes           | no           | perf       | warn  |

