## G.MAC.01 `dbg!()` 宏只应该用于调试代码

**【级别】** 建议

**【描述】**

`dbg!()` 宏是 Rust 内置的宏，其目的是用于调试代码。 不要将含有 dbg! 宏的代码加入到版本控制下。

注意：不管在 Debug 模式还是 Release 模式下，调试信息都会被打印出来。

**【反例】**

```rust
// Release 模式编译
let foo = false;
dbg!(foo); 
```

**【正例】**

```rust
// Debug 模式编译
let foo = false;
dbg!(foo); 

// Release 模式编译
let foo = false;
// dbg!(foo); 
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [dbg_macro](https://rust-lang.github.io/rust-clippy/master/#dbg_macro) | yes           | no           | restriction | allow |

