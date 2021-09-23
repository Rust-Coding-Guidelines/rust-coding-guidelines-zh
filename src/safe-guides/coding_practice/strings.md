# 字符串

Rust 中字符串是有效的 UTF-8 编码的字节数组。

Rust 字符串类型众多，但本节内容主要围绕 ：`String` / `&str`  

---

## P.STR.01 

【描述】



---



## G.STR.01   要对字符串进行比较

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [bytes_nth](https://rust-lang.github.io/rust-clippy/master/#bytes_nth) | yes           | no           | style      | warn  |

### 【描述】

