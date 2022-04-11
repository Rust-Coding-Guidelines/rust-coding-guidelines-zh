## G.CTF.02 `if`条件表达式分支中如果包含了`else if`分支也应该包含`else`分支

**【级别】** 建议

**【描述】**

这样做有助于代码逻辑更加健壮清晰，在一些要求严格的编码规范中要求这么做，比如《MISRA-C:2004 Rule 14.10》编码规范。

**【反例】**

```rust
#[warn(clippy::else_if_without_else)]
fn a() {}
fn b() {}

fn main(){
    let x: i32 = 1;
    if x.is_positive() {
        a();
    } else if x.is_negative() {
        b();
    } // 不符合：没有 else 分支
}
```

**【正例】**

```rust
#[warn(clippy::else_if_without_else)]
fn a() {}
fn b() {}

fn main(){
    let x: i32 = 1;
    if x.is_positive() {
        a();
    } else if x.is_negative() {
        b();
    } else {
        // 符合
    }
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group      | level |
| ------------------------------------------------------------ | ------------- | ------------ | --------------- | ----- |
| [else_if_without_else](https://rust-lang.github.io/rust-clippy/master/#else_if_without_else) | yes           | no           | **restriction** | allow |



