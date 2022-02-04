## P.GEN.02  不要随便使用 `impl Trait` 语法替代泛型限定

**【描述】**

`impl Trait` 语法 和 泛型限定，虽然都是静态分发，且效果类似，但是它们的语义是不同的。

**在类型系统层面上的语义：**

1. `impl Trait` 是 存在量化类型。意指，存在某一个被限定的类型。
2. 泛型限定 是 通用量化类型。意指，所有被限定的类型。

要根据它们的语义来选择不同的写法。

另外，`impl Trait` 可以用在函数参数位置和返回值位置，但是不同位置意义不同。

**函数参数位置**

等价于 泛型参数。

但要注意：

```rust
fn f(b1: impl Bar, b2: impl Bar) -> usize
```

 等价于：

```rust
fn f<B1: Bar, B2: Bar>(b1: B1, b2: B2) -> usize
```

  而不是

```rust
fn f<B: Bar>(b1: B, b2: B) -> usize
```

证明示例：

```rust
use std::fmt::Display;

// 函数参数可以传入 整数，但是函数返回值是 String
fn func(arg: impl Display) -> impl Display {
    format!("Hay! I am not the same as \"{}\"", arg)
}

// 很明显不等价于下面这类
// fn somefunc2<T: Display>(arg: T) -> T {
//     // 需要指定同一个类型 T 的行为
// }

fn main(){
    let a  = 42;
    let a = func(42);
}
```

**函数返回值**

在返回值位置上，如果是泛型参数，则是由调用者来选择具体类型，比如 `parse::<i32>("32")` ； 如果是 `impl Trait`，则是由被调用者来决定具体类型，但只能有一种类型。

在返回值位置上的 `impl Trait` 会根据函数体的返回值自动推断实现了哪些  auto trait。这意味着你不必在 `impl Trait` 后面再 加 `Sync + Send ` 这种auto trait。

注意下面代码：

```rust
// Error： 这里只允许有同一种具体类型，Foo 和 Baz 都实现了 Bar 也是错的。
fn f(a: bool) -> impl Bar {
    if a {
        Foo { ... }
    } else {
        Baz { ... }
    }
}
```