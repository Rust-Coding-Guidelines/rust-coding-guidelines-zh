## G.STR.04   需要判断字符串以哪个字符开头或结尾时，不要按字符迭代比较

**【级别】** 建议

**【描述】**

Rust 语言 核心库 和 标准库都对字符串内置了一些方便的方法来处理这类需求。

迭代字符的性能虽然也很快（对500多个字符迭代转义处理大概需要4.5微秒左右），但这种场景用迭代的话，代码可读性更差一些。

**【反例】**

```rust
let name = "_";
name.chars().last() == Some('_') || name.chars().next_back() == Some('-');

let name = "foo";
if name.chars().next() == Some('_') {};
```

**【正例】**

```rust
let name = "_";
name.ends_with('_') || name.ends_with('-');

let name = "foo";
if name.starts_with('_') {};
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [chars_last_cmp](https://rust-lang.github.io/rust-clippy/master/#chars_last_cmp) | yes           | no           | style      | warn  |
| [chars_next_cmp](https://rust-lang.github.io/rust-clippy/master/#chars_next_cmp) | yes           | no           | style      | warn  |




