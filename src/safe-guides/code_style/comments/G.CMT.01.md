## G.CMT.01 在公开的返回`Result`类型的函数文档中增加 Error 注释

**【级别】** 建议

**【描述】**

在公开（pub）的返回`Result`类型的函数文档中，建议增加 `# Error` 注释来解释什么场景下该函数会返回什么样的错误类型，方便用户处理错误。

说明： 该规则可以通过 cargo clippy 来检测，但默认不会警告。

**【反例】**


```rust
#![warn(clippy::missing_errors_doc)]

use std::io;
// 不符合： Clippy 会警告 "warning: docs for function returning `Result` missing `# Errors` section"
pub fn read(filename: String) -> io::Result<String> {
    unimplemented!();
}
```

**【正例】**

```rust
#![warn(clippy::missing_errors_doc)]

use std::io;
// 符合：增加了规范的 Errors 文档注释

/// # Errors
///
/// Will return `Err` if `filename` does not exist or the user does not have
/// permission to read it.
pub fn read(filename: String) -> io::Result<String> {
    unimplemented!();
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认 level |
| ------ | ---- | --------- | ------ | ------ | 
| [missing_errors_doc ](https://rust-lang.github.io/rust-clippy/master/index.html#missing_errors_doc ) | yes| no | Style | allow | 