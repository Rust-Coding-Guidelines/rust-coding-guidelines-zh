## G.MTH.LOK.02 建议使用 `Arc<str> / Arc<[T]>` 来代替  `Arc<String> / Arc<Vec<T>>`

**【级别】** 建议

**【描述】**

`Arc<str> / Arc<[T]>` 的性能比  `Arc<String> / Arc<Vec<T>>` 更好。

因为 ：

- `Arc<String> / Arc<Vec<T>>` 有一层中间层： `arc -> String len/Vec<T> len -> text/data`，它是一个薄指针（thin pointer）。
- `Arc<str>/ Arc<[T]>` 则没有中间层： `arc & string len / [T] len -> text/data`，它是一个胖指针（fat pointer）。

**【反例】**

```rust
use std::rc::Rc;
use std::sync::Arc;

fn main() {
    let a = "hello world".to_string();
    let b: Rc<String> = Rc::from(a);
    println!("{}", b);

    // or equivalently:
    let a = "hello world".to_string();
    let b: Rc<String> = a.into();
    println!("{}", b);

    // we can also do this for Arc,
    let a = "hello world".to_string();
    let b: Arc<String> = Arc::from(a);
    println!("{}", b);
}
```

**【正例】**

```rust
use std::rc::Rc;
use std::sync::Arc;

fn main() {
    let a: &str = "hello world";
    let b: Rc<str> = Rc::from(a);
    println!("{}", b);

    // or equivalently:
    let b: Rc<str> = a.into();
    println!("{}", b);

    // we can also do this for Arc,
    let a: &str = "hello world";
    let b: Arc<str> = Arc::from(a);
    println!("{}", b);
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [rc_buffer](https://rust-lang.github.io/rust-clippy/master/#rc_buffer) | yes           | no           | restriction | allow |
