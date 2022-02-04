## G.CTF.06 善用标准库中提供的迭代器适配器方法来满足自己的需求

**【级别】** 建议

**【描述】**

Rust 标准库中提供了很多迭代器方法，要学会使用它们，选择合适的方法来满足自己的需求。

下面示例中，反例中的迭代器适配器方法，都可以用对应的正例中的方法代替。

**【反例】**

```rust
// explicit_counter_loop
let v = vec![1];
fn bar(bar: usize, baz: usize) {}
let mut i = 0;
for item in &v {
    bar(i, *item);
    i += 1;
}

// filter_map_identity
let iter = vec![Some(1)].into_iter();
iter.filter_map(|x| x);

// filter_next
let vec = vec![1];
vec.iter().filter(|x| **x == 0).next();

// flat_map_identity
let iter = vec![vec![0]].into_iter();
iter.flat_map(|x| x);

// flat_map_option
let nums: Vec<i32> = ["1", "2", "whee!"].iter().flat_map(|x| x.parse().ok()).collect();
```

**【正例】**

```rust
// explicit_counter_loop
let v = vec![1];
fn bar(bar: usize, baz: usize) {}
for (i, item) in v.iter().enumerate() { bar(i, *item); }

// filter_map_identity
let iter = vec![Some(1)].into_iter();
iter.flatten();

// filter_next
let vec = vec![1];
vec.iter().find(|x| **x == 0);

// flat_map_identity
let iter = vec![vec![0]].into_iter();
iter.flatten();

// flat_map_option
let nums: Vec<i32> = ["1", "2", "whee!"].iter().filter_map(|x| x.parse().ok()).collect();

```
**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [explicit_counter_loop](https://rust-lang.github.io/rust-clippy/master/#explicit_counter_loop) | yes           | no           | complexity | warn  |
| [filter_map_identity](https://rust-lang.github.io/rust-clippy/master/#filter_map_identity) | yes           | no           | complexity | warn  |
| [filter_next](https://rust-lang.github.io/rust-clippy/master/#filter_next) | yes           | no           | complexity | warn  |
| [flat_map_identity](https://rust-lang.github.io/rust-clippy/master/#flat_map_identity) | yes           | no           | complexity | warn  |
| [flat_map_option](https://rust-lang.github.io/rust-clippy/master/#flat_map_option) | yes           | no           | pedantic | allow  |


