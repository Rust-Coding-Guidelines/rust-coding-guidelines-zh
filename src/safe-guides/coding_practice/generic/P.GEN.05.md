## P.GEN.05 定义泛型函数时，如果该函数实现用到来自 trait 定义的相关行为，需要为泛型指定相关 trait 的限定

**【描述】**

泛型，在 Rust 类型系统中的语义是一种 通用量化l类型（Universally-quantified type），即，泛型类型` T` 的所有可能 的单态类型。

在泛型函数内部，如果使用了来自某个 trait 定义的行为，则需要为泛型指定相关的 trait 限定，来排除其他没有实现该trait 的类型。

注：Rust编译器可以检测这种情况，但是编译错误比较晦涩，本原则用来提示开发者注意这种情况。

**【反例】**

```rust
use std::fmt;

// println! 中 `{:?}` 为 Debug triat 定义行为
fn some_func<T>(foo: T) {
    println!("{:?}", foo); // error[E0277]: `T` doesn't implement `Debug`
}
```

**【正例】**

```rust
use std::fmt;

// 为泛型类型 T 指派 Debug triat 限定
fn some_func<T: fmt::Debug>(foo: T) {
    println!("{:?}", foo); 
}

struct A;

fn main() {
   some_func(5i32);
   
   // A 没有实现 Debug trait，会被排除掉
   some_func(A); // error[E0277]: `A` doesn't implement `Debug`
}
```