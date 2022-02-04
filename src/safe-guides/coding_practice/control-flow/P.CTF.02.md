## P.CTF.02  优先使用模式匹配而不是判断后再取值

**【描述】**

Rust 中 模式匹配 是惯用法，而不是通过 `if` 判断值是否相等。

**【反例】**

 ```rust
 let opt: Option<_> = ...;
 
 if opt.is_some() {
   let value = opt.unwrap();
   ...
 }
 
 // or
 let list: &[f32] = ...;
 
 if !list.is_empty() {
   let first = list[0];
   ...
 }
 
 ```

**【正例】**

```rust
if let Some(value) = opt {
  ...
}
// or
if let [first, ..] = list {
  ...
}
```
---

