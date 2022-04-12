## G.MTH.LCK.02 宜使用 `Arc<str> / Arc<[T]>` 来代替  `Arc<String> / Arc<Vec<T>>`

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
    let b: Rc<String> = Rc::from(a); // 不符合
    println!("{}", b);

    let a = "hello world".to_string();
    let b: Rc<String> = a.into(); // 不符合
    println!("{}", b);

    let a = "hello world".to_string();
    let b: Arc<String> = Arc::from(a); // 不符合
    println!("{}", b);
}
```

**【正例】**

```rust
use std::rc::Rc;
use std::sync::Arc;

fn main() {
    let a: &str = "hello world";
    let b: Rc<str> = Rc::from(a); // 符合
    println!("{}", b);

    let b: Rc<str> = a.into(); // 符合
    println!("{}", b);

    let a: &str = "hello world";
    let b: Arc<str> = Arc::from(a); // 符合
    println!("{}", b);
}
```

**【例外】**

参考：[https://github.com/rust-lang/rust-clippy/pull/6044#issuecomment-699565080](https://github.com/rust-lang/rust-clippy/pull/6044#issuecomment-699565080)

```rust
// From: https://github.com/Fishrock123/surf/blob/master/src/client.rs#L33

pub struct Client {
    http_client: Arc<dyn HttpClient>,
    /// Holds the middleware stack.
    // 业务上必须要求持有一个 Vec 才能保证用户正常添加中间件
    middleware: Arc<Vec<Arc<dyn Middleware>>>,
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [rc_buffer](https://rust-lang.github.io/rust-clippy/master/#rc_buffer) | yes           | no           | restriction | allow |
