## G.EXP.03 不应利用数组表达式的边界检查来 Panic，而应使用断言

**【级别】** 建议

**【描述】**

这样会影响代码可读性。使用断言可以更好的描述代码的意图。

**【反例】**

```rust
fn main(){
   // 不符合
   [42, 55][get_usize()];
   compute_array()[0];
}

fn get_usize() -> usize {
   6
}

fn compute_array() -> [i32; 3] {
   [1,2,3]
}
```

**【正例】**

```rust
fn main(){
   // 符合
   assert!([42, 55].len() > get_usize());
   assert!(compute_array().len() > 0);
}

fn get_usize() -> usize {
   6
}

fn compute_array() -> [i32; 3] {
   [1,2,3]
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group     | level |
| ------------------------------------------------------------ | ------------- | ------------ | -------------- | ----- |
| [unnecessary_operation](https://rust-lang.github.io/rust-clippy/master/#unnecessary_operation) | yes           | no           | **complexity** | warn  |


