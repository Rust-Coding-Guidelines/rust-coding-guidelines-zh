# 控制流程

Rust 中 流程控制 也是属于 表达式，但在本规范中将其独立出来。

---

P.CTL.01 

【描述】


---


## G.CTL.01  避免在流程控制分支中使用重复代码

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [branches_sharing_code](https://rust-lang.github.io/rust-clippy/master/#branches_sharing_code) | yes| no | nursery | allow |

### 【描述】

【正例】

```rust
println!("Hello World");
let foo = if … {
    13
} else {
    42
};
```

【反例】

```rust
let foo = if … {
    println!("Hello World");
    13
} else {
    println!("Hello World");
    42
};
```