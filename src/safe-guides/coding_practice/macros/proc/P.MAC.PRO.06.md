## P.MAC.PRO.06 `build.rs` 生成的代码要保证没有任何警告

**【描述】**

`build.rs` 生成的代码(codegen)，要通过或忽略 clippy 检查，不要让库的使用者或应用用户自行忽略

codegen 库要保证生成的代码应该非常干净没有任何警告，不应该让库的使用者去处理生成代码中的警告。

**【反例】**

lalrpop v0.19.6 生成的代码有几百个 clippy 警告，"淹没"了用户自己代码的 clippy 警告

```
warning: using `clone` on type `usize` which implements the `Copy` trait
      --> /home/w/temp/my_parser/target/debug/build/my_parser-dd96f436ee76c58d/out/my_parser.rs:182148:21
       |
182148 |         let __end = __start.clone();
       |                     ^^^^^^^^^^^^^^^ help: try removing the `clone` call: `__start`
```

使得 lalrpop 库的使用者必须手动给生成的模块代码加上 allow clippy，给使用者带来不便

```rust
lalrpop_mod!(
    #[allow(clippy::all)]
    my_parser
);
```

**【正例】**

tonic-build 生成的 rs 会通过 allow 忽略掉 clippy 警告

```rust
pub mod peer_communication_client {
    #![allow(unused_variables, dead_code, missing_docs, clippy::let_unit_value)]
    use tonic::codegen::*;
```
