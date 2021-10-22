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





## G.STR.02   在实现  `Display` trait 时不要调用 `to_string()` 方法

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [to_string_in_display](https://rust-lang.github.io/rust-clippy/master/#to_string_in_display) | yes           | no           | complexity | warn  |

### 【描述】

因为 `to_string` 是间接通过 `Display` 来实现的，如果实现 `Display` 的时候再使用 `to_tring` 的话，将会无限递归。

【正例】

```rust
use std::fmt;

struct Structure(i32);
impl fmt::Display for Structure {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "{}", self.0)
    }
}
```

【反例】

```rust
use std::fmt;

struct Structure(i32);
impl fmt::Display for Structure {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "{}", self.to_string())
    }
}
```

## G.STR.03   在拼接字符串时使用 `push_str`方法可读性更强

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [string_add_assign](https://rust-lang.github.io/rust-clippy/master/#string_add_assign) | yes           | no           | pedantic    | allow |
| [string_add](https://rust-lang.github.io/rust-clippy/master/#string_add) | yes           | no           | restriction | allow |

### 【描述】





## G.STR.04    将只包含 `ASCII`字符的字符串字面量转为字节序列可以直接使用`b"str"` 语法代替调用`as_bytes`方法

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [string_lit_as_bytes](https://rust-lang.github.io/rust-clippy/master/#string_lit_as_bytes) | yes           | no           | complexity | warn  |

### 【描述】

这是为了增强可读性，让代码更简洁。

注意，`"str".as_bytes()` 并不等价于  `b"str"`，而是等价于 `&b"str"[..]`  。

【正例】

```rust
let bs = b"a byte string";
```

【反例】

```rust
let bs = "a byte string".as_bytes();
```

