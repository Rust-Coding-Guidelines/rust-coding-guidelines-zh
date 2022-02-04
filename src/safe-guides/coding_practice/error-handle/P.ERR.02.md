## P.ERR.02  当函数的返回值或者结构体字段的值可能为空时，请使用`Option<T>`

**【描述】**

在某些其他语言中，如果函数的返回值 或 结构体字段的值 可能为空时，通常会设置一个 “哨兵值（Sentinel Value）” 来应对这种问题，比如使用一个 `nil` 或 `-1` 等特殊值来判断这类情况。

但是，在 Rust 中不需要这样，Rust 提供了 `Option<T>` 类型就是专门用于应对这类情况。

**【正例】**

```rust
struct Config {
    must: String,
    opt: Option<String>,
}

// OR

fn main() {
    let sentence = "The fox jumps over the dog";
    let index = sentence.find("fox");

    if let Some(fox) = index {
        let words_after_fox = &sentence[fox..];
        println!("{}", words_after_fox);
    }
}
```