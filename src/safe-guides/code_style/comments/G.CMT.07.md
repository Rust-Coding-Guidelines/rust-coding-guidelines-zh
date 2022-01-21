## G.CMT.07  在 文档注释中要使用 空格代替 tab

**【级别】** 建议

**【描述】**

Rust 代码风格中提倡使用空格代替tab，在文档注释中也应该统一使用空格。

**【反例】**

下面文档注释中使用了 tab。

```rust
///
/// Struct to hold two strings:
/// 	- first		one
/// 	- second	one
pub struct DoubleString {
   ///
   /// 	- First String:
   /// 		- needs to be inside here
   first_string: String,
   ///
   /// 	- Second String:
   /// 		- needs to be inside here
   second_string: String,
}
```

**【正例】**

```rust
///
/// Struct to hold two strings:
///     - first        one
///     - second    one
pub struct DoubleString {
   ///
   ///     - First String:
   ///         - needs to be inside here
   first_string: String,
   ///
   ///     - Second String:
   ///         - needs to be inside here
   second_string: String,
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认 level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ---------- |
| [tabs_in_doc_comments ](https://rust-lang.github.io/rust-clippy/master/index.html#tabs_in_doc_comments ) | yes           | no           | Style      | warn       |

---