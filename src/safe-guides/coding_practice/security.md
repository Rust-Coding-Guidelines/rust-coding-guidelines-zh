# Security

Security 用于规范可能引起信息安全（Security）缺陷的代码实现，而非功能安全（ Safety）类问题。

---

## G.Security.01  代码中不要出现非法 Unicode 字符，也要防范非法 Unicode 字符

### 【级别： 必须】

必须按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [`invisible_characters`](https://rust-lang.github.io/rust-clippy/master/#invisible_characters) | yes           | no           | correctness | deny  |
| [`text-direction-codepoint-in-comment`](https://doc.rust-lang.org/rustc/lints/listing/deny-by-default.html#text-direction-codepoint-in-comment) | no            | yes          | -           | deny  |
| [`text_direction_codepoint_in_literal`](https://doc.rust-lang.org/rustc/lints/listing/deny-by-default.html#text-direction-codepoint-in-literal) | no            | yes          | -           | deny  |
| [`confusable_idents`](https://doc.rust-lang.org/rustc/lints/listing/warn-by-default.html#confusable-idents) | no            | yes          | -           | warn  |
| [`mixed_script_confusables`](https://doc.rust-lang.org/rustc/lints/listing/warn-by-default.html#mixed-script-confusables) | no            | yes          | -           | warn  |

### 【描述】

非法 Unicode 字符可能引起安全问题。安全问题参见： [Rust 编译器安全公告（CVE-2021-42574）](https://blog.rust-lang.org/2021/11/01/cve-2021-42574.html) 

禁止的 Unicode 字符类别为：

1.  隐藏的 Unicode 字符
2. 双向 Unicode 字符文本
3. 同形 Unicode 字符

Clippy Lint  目前只可以检测代码中出现的隐藏 Unicode 字符。

在 Rust 1.56.1 之后 新增两个 `lint` 拒绝代码中出现可以更改显示顺序的 `Unicode` 码点出现。并且特别禁止 `\u{202A}`，`\u{202B}`，`\u{202D}`，`\u{202E}`，`\u{2066}`， `\u{2067}`，`\u{2068}`，`\u{202C}`和`\u{2069}` 这几个特殊的 `Unicode` 码点。

Rust 的 `mixed_script_confusables` 和 `confusable_idents` 可以识别 同形字符。 

写代码的时候需要注意，尤其是开源代码，需要防范上述非法 Unicode 字符。

【正例】

```rust
#![deny(text_direction_codepoint_in_comment)]
fn main() {
    println!("{:?}"); // '‮');
}

#![deny(text_direction_codepoint_in_literal)]
fn main() {
    println!("{:?}", '‮');
}
```

【例外】

但也有例外，比如你的代码恰好是要处理这些特殊Unicode字符的。

```rust
// From: https://docs.rs/crate/lingo/0.1.2/source/src/generated.rs
#[allow(clippy::invisible_characters)]
pub fn get_embed_languages() -> FileContent {
    let mut f = FileContent::from_vec(vec![
        (
            Language::Afrikaans.name(),
            vec![
                "e", "a", "i", "n", "s", "r", "o", "t", "d", "e_", "l", "k", "g", "ie", "n_",
                // 省略很多字符，包括特殊的隐藏 unicode 字符
            ]
        )
    )
 }
```







