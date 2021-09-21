# 枚举体

Rust 的枚举是一种带 Tag 的联合体。

Rust 中枚举体用处很多，你甚至可以将其作为一种接口使用。

---

## P.TYP.Enum.01 

【描述】


--- 


## G.TYP.Enum.01 要避免使用`and_then`而使用`map`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [bind_instead_of_map ](https://rust-lang.github.io/rust-clippy/master/#bind_instead_of_map ) | yes| no | complexity | warn |

### 【描述】

为了让代码更加简单明了增强可读性，建议使用 `map`。

【正例】

```rust
# fn opt() -> Option<&'static str> { Some("42") }
# fn res() -> Result<&'static str, &'static str> { Ok("42") }
let _ = opt().map(|s| s.len());
let _ = res().map(|s| if s.len() == 42 { 10 } else { 20 });
let _ = res().map_err(|s| if s.len() == 42 { 10 } else { 20 });
```

【反例】

```rust
# fn opt() -> Option<&'static str> { Some("42") }
# fn res() -> Result<&'static str, &'static str> { Ok("42") }
let _ = opt().and_then(|s| Some(s.len()));
let _ = res().and_then(|s| if s.len() == 42 { Ok(10) } else { Ok(20) });
let _ = res().or_else(|s| if s.len() == 42 { Err(10) } else { Err(20) });

```