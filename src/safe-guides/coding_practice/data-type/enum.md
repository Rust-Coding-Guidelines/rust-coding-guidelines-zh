# 枚举体

Rust 的枚举是一种带 Tag 的联合体。

Rust 中枚举体用处很多，你甚至可以将其作为一种接口使用。

---

## P.TYP.Enum.01  不要自己创建空枚举

【描述】

在 Rust 中 只有 `never` 类型（`!`）才是唯一合法表达 无法被实例化类型 的类型。但目前 `never` 类型还未稳定，只能在 Nightly 下使用。

所以，如果想在 稳定版 Rust 中使用，建议使用[`std::convert::Infallible`](https://doc.rust-lang.org/std/convert/enum.Infallible.html#) 。 `Infallible` 枚举是一个合法的 空枚举，常用于错误处理中，表示永远不可能出现的错误。但是目前也可以用于在稳定版中替代  `never`   类型。

```rust
// 未来 never 类型稳定的话，将会把 Infallible 设置为 never 类型的别名
pub type Infallible = !;
```






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



## G.TYP.Enum.02  不要自己创建空枚举

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group   | level |
| ------------------------------------------------------------ | ------------- | ------------ | ------------ | ----- |
| [empty_enum](https://rust-lang.github.io/rust-clippy/master/#empty_enum) | yes           | no           | **pedantic** | allow |

### 【描述】