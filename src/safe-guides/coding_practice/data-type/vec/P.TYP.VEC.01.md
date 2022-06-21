## P.TYP.VEC.01 非必要时不宜使用动态数组

**【描述】**

非必须不宜使用 `Vec<T>`，应该优先尝试使用固定长度数组或常量泛型。

或者可以参考第三方库，诸如 [`smallvec`](https://docs.rs/smallvec/latest/smallvec) ，在元素比较少量的时候，可以放到栈上进行管理，如果超过一定元素才会选择堆内存。

**【反例】**

```rust
fn main() {
    // 不符合
    let v: Vec<i32> = vec![1, 2, 3];
    println!("{:#}", v);
}
```

**【正例】**

```rust
fn main() {
    // 符合
    let v = [1, 2, 3];
    println!("{:#?}", v);
}
```

