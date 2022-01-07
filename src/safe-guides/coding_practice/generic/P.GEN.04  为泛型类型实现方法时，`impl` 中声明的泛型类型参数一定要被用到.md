## P.GEN.04  为泛型类型实现方法时，`impl` 中声明的泛型类型参数一定要被用到

**【描述】**

在 `impl` 中被声明的类型参数，至少要满足下面三种形式：

1. `impl<T> Foo<T>`，  `T`  出现在实现的`Self` 类型`Foo<T>` 中 。
2. `impl<T> SomeTrait<T> for Foo` ， `T`出现在要实现的 trait 中 。
3. `impl<T, U> SomeTrait for T where T: AnotherTrait<AssocType=U>` ， 出现在 `T` 的 trait 限定的关联类型中。

除此之外，都不算 `T` 被用到（出现在 Self 类型中）。

有这种限制，主要有两个原因：

1. 方便 Rust 类型推断。有这些限制才能明确能推断这些泛型参数的行为，避免产生错误。参考 [RFC 0447 ](https://github.com/rust-lang/rfcs/blob/master/text/0447-no-unused-impl-parameters.md) 。
2. 避免语义定义不明确的情况。如果 `impl` 上存在自由的 泛型参数，则无法保证这一点。

**【反例】**

```rust
// case 1

struct Foo;

impl<T: Default> Foo {
    // error: the type parameter `T` is not constrained by the impl trait, self
    // type, or predicates [E0207]
    fn get(&self) -> T {
        <T as Default>::default()
    }
}

// case 2
trait Maker {
    type Item;
    fn make(&mut self) -> Self::Item;
}
struct Foo<T> {
    foo: T
}
struct FooMaker;
impl<T: Default> Maker for FooMaker {
// error: the type parameter `T` is not constrained by the impl trait, self
// type, or predicates [E0207]
    type Item = Foo<T>;

    fn make(&mut self) -> Foo<T> {
        Foo { foo: <T as Default>::default() }
    }
}

// error: the type parameter `A` is not constrained by the impl trait, self type, or predicates
trait Foo {}
impl<F, A> Foo for F where F: Fn(A) {} // error
```

**【正例】**

```rust
// case 1
struct Foo;

// Move the type parameter from the impl to the method
impl Foo {
    fn get<T: Default>(&self) -> T {
        <T as Default>::default()
    }
}

// case 2
use std::marker::PhantomData;

trait Maker {
    type Item;
    fn make(&mut self) -> Self::Item;
}

struct Foo<T> {
    foo: T
}

// Add a type parameter to `FooMaker`
struct FooMaker<T> {
    phantom: PhantomData<T>,
}

impl<T: Default> Maker for FooMaker<T> {
    type Item = Foo<T>;

    fn make(&mut self) -> Foo<T> {
        Foo {
            foo: <T as Default>::default(),
        }
    }
}

// closue : 相关 issue:  https://github.com/rust-lang/rust/issues/25041 
trait Foo {}
impl<F, A> Foo for F where F: Fn() -> A {} // 此处 A 是 闭包trait内的一个关联类型
```
