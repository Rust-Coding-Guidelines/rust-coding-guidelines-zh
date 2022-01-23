## G.NAM.8  避免使用语言内置保留字、关键字、内置类型和`trait`等特殊名称

### 【级别：建议】

### 【描述】

命名必须要避免使用语言内置的保留字、关键字、内置类型和`trait`等特殊名称。 具体可以参考[The Rust Reference-Keywords](https://doc.rust-lang.org/reference/keywords.html)

### 【反例】

```rust
// Sized ： Rust 内置了同名 trait 
type Sized = u16; 

fn main() {
    // try 为保留关键字，使用`r#`前缀可以使用它，但要尽力避免
    let r#try = 1;
}
```

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制化参考】

可以检测 标识符 是否通过`r#`使用了 语言内置的保留字、关键字、内置类型和`trait`等特殊名称，如果使用，则给予警告。