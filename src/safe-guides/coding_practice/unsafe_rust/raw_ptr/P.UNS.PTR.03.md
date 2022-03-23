## P.UNS.PTR.03  使用指针类型构造泛型结构体时，需要使用 `PhantomData<T>` 来指定 `T`上的协变和所有权

**【描述】**

`PhantomData<T>` 是经常被用于 Unsafe Rust 中配合裸指针来指定协变和所有权的，为裸指针构建的类型保证安全性和有效性。否则，可能会产生未定义行为。

参考： [`PhantomData<T>`  的型变（variance）模式表](https://doc.rust-lang.org/nomicon/phantom-data.html) 

**【反例】**

```rust
// Vec<T> 不拥有类型 T，并且 data 字段的裸指针不支持协变
// 这样的话，是有风险的。
// 为 Vec<T> 实现的 Drop 可能导致 UB
struct Vec<T> {
    data: *const T, 
    len: usize,
    cap: usize,
}
```

**【正例】**

```rust
use std::marker;

struct Vec<T> {
    data: *const T, // *const for variance!
    len: usize,
    cap: usize,
    _marker: marker::PhantomData<T>, // 让 Vec<T> 拥有 T，并且让 指针有了协变
}
```