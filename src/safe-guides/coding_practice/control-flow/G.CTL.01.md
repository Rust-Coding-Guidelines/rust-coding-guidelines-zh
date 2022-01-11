## G.CTL.01 避免在流程控制分支中使用重复代码

**【级别】** 建议

**【描述】**

略

**【反例】**

```rust
let foo = if … {
    println!("Hello World");
    13
} else {
    println!("Hello World");
    42
};
```

**【正例】**

```rust
println!("Hello World");
let foo = if … {
    13
} else {
    42
};
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ |
| [branches_sharing_code](https://rust-lang.github.io/rust-clippy/master/#branches_sharing_code) | yes| no | nursery | allow |



