# 泛型

Rust 中的泛型允许开发人员编写更加简洁、更少重复的代码。但泛型可能会引起编译文件大小膨胀，酌情使用。

---

## P.GEN.01    用泛型来抽象公共语义

**【描述】**

应该巧用泛型来抽象公共语义，消除重复代码。

【正例】

```rust
use std::ops::Add;
use std::marker::PhantomData;

#[derive(Debug, Clone, Copy)]
struct Unit<T> {
    value: f64,
    unit_type: PhantomData<T>,
}

impl<T> Unit<T> {
    fn new(value: f64) -> Self {
        Self {
            value,
            unit_type: PhantomData,
        }
    }
}

impl<T> Add for Unit<T> {
    type Output = Unit<T>;

    fn add(self, another: Unit<T>) -> Self::Output {
        let new_value = self.value + another.value;
        Unit::new(new_value)
    }
}

#[derive(Debug, Clone, Copy)]
struct MeterType;

#[derive(Debug, Clone, Copy)]
struct KilogramType;

type Meter = Unit<MeterType>;
type Kilogram = Unit<KilogramType>;
fn main() {
    let one_meter = Meter::new(1.0);
    let two_kilograms = Kilogram::new(2.0);
    
    let two_meters = one_meter + one_meter;
}
```

【反例】

```rust
use std::ops::Add;

#[derive(Debug, Clone, Copy)]
struct Meter {
    value: f64
}

impl Meter {
    fn new(value: f64) -> Self {
        Self { value }
    }
}

impl Add for Meter {
    type Output = Meter;

    fn add(self, another: Meter) -> Self::Output {
        let value = self.value + another.value;
        Meter { value }
    }
}

#[derive(Debug, Clone, Copy)]
struct Kilogram {
    value: f64
}

impl Kilogram {
    fn new(value: f64) -> Self {
        Self { value }
    }
}

impl Add for Kilogram {
    type Output = Kilogram;

    fn add(self, another: Kilogram) -> Self::Output {
        let value = self.value + another.value;
        Kilogram { value }
    }
}
fn main() {
    let one_meter = Meter::new(1.0);
    let two_kilograms = Kilogram::new(2.0);
    
    let two_meters = one_meter + one_meter;
}

```



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



## P.GEN.03   不要使用太多泛型参数和 trait 限定，否则会增长编译时间

**【描述】**

为泛型函数添加详细的trait 限定，可以在一定程度上增强用户使用体验，但使用过多的泛型参数和 trait 限定会显著地增长编译时间。

【正例】

来自于 Web 框架 Axum 的代码：

```rust
    // From: https://github.com/tokio-rs/axum/pull/198
    fn handle_error<ReqBody, F>(
        self,
        f: F,
    ) -> HandleError<Self, F, ReqBody, HandleErrorFromRouter> {
        HandleError::new(self, f)
    }   
```

【反例】

以下写法比上面的写法编译时间要多十倍。

```rust
    // From: https://github.com/tokio-rs/axum/pull/198
    fn handle_error<ReqBody, ResBody, F, Res, E>(
        self,
        f: F,
    ) -> HandleError<Self, F, ReqBody, HandleErrorFromRouter>
    where
        Self: Service<Request<ReqBody>, Response = Response<ResBody>>,
        F: FnOnce(Self::Error) -> Result<Res, E>,
        Res: IntoResponse,
        ResBody: http_body::Body<Data = Bytes> + Send + Sync + 'static,
        ResBody::Error: Into<BoxError> + Send + Sync + 'static,
    {
        HandleError::new(self, f)
    }
```




---

## G.GEN.01     泛型参数必须先声明再使用

### 【级别：规则】

按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | yes          | _          | _          |

### **【描述】**

泛型参数必须先被声明，才能被使用。如果一个类型中包含泛型类型，也必须为其声明泛型参数。

【正例】

```rust
struct Foo<T> { x: T }

struct Bar<T> { x: Foo<T> }
```

【反例】

```rust
struct Foo<T> { x: T }

struct Bar { x: Foo } // error[E0107]: missing generics for struct `Foo`
```

## G.GEN.02   不要在泛型位置上使用内建类型

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [builtin_type_shadow](https://rust-lang.github.io/rust-clippy/master/#builtin_type_shadow) | yes           | no           | style      | warn  |

### 【描述】


【正例】

```rust
impl<T> Foo<T> {
    fn impl_func(&self) -> T {
        42
    }
}
```

【反例】

这里 `u32` 会被认为是一个类型参数。

```rust
impl<u32> Foo<u32> {
    fn impl_func(&self) -> u32 {
        42
    }
}
```

## G.GEN.03   使用 Rust 标准库中某些方法，要注意避免使用其泛型默认实现，而应该使用具体类型的实现

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [inefficient_to_string](https://rust-lang.github.io/rust-clippy/master/#inefficient_to_string) | yes           | no           | pedantic   | allow |

### 【描述】

Rust 标准库内部某些类型使用了 泛型特化（未稳定特性），比如 `ToString` trait。

该 trait 有一个[泛型默认实现](https://doc.rust-lang.org/stable/src/alloc/string.rs.html#2369)， 并且一些具体类型也实现了它，比如 `char`/ `str` /  `u8`/ `i8` 等。

在实际代码中，应该选择去调用具体类型实现的 `to_string()` 方法，而非调用泛型的默认实现。 

**这一规则要求开发者对 Rust 标准库的一些方法实现有一定了解。** 

【正例】

```rust
// 闭包参数中， s 为 `&&str` 类型，使用 `|&s|` 对参数模式匹配后，闭包体内 `s` 就变成了 `&str` 类型
// 经过这样的转换，直接调用 `&str`的 `to_string()` 方法，而如果是 `&&str` 就会去调用泛型的默认实现。 
["foo", "bar"].iter().map(|&s| s.to_string() );
```

【反例】

```rust
// 闭包参数中， s 为 `&&str` 类型
//  `&&str` 就会去调用泛型的默认实现
["foo", "bar"].iter().map(|&s| s.to_string() );
```

## G.GEN.04  为泛型类型实现方法时，`impl` 中声明的泛型类型参数一定要被用到

### 【级别：规则】

按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | yes          | _          | _          |

### **【描述】**

在 `impl` 中被声明的类型参数，至少要满足下面三种形式：

1. `impl<T> Foo<T>`，  `T`  出现在实现的`Self` 类型`Foo<T>` 中 。
2. `impl<T> SomeTrait<T> for Foo` ， `T`出现在要实现的 trait 中 。
3. `impl<T, U> SomeTrait for T where T: AnotherTrait<AssocType=U>` ， 出现在 `T` 的 trait 限定的关联类型中。

除此之外，都不算 `T` 被用到（出现在 Self 类型中）。

有这种限制，主要有两个原因：

1. 方便 Rust 类型推断。有这些限制才能明确能推断这些泛型参数的行为，避免产生错误。参考 [RFC 0447 ](https://github.com/rust-lang/rfcs/blob/master/text/0447-no-unused-impl-parameters.md) 。
2. 避免语义定义不明确的情况。如果 `impl` 上存在自由的 泛型参数，则无法保证这一点。

【正例】

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

【反例】

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

## G.GEN.05   定义泛型函数时，如果该函数实现用到来自 trait 定义的相关行为，需要为泛型指定相关 trait 的限定

### 【级别：规则】

按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | yes          | _          | _          |

### **【描述】**

泛型，在 Rust 类型系统中的语义是一种 通用量化l类型（Universally-quantified type），即，泛型类型` T` 的所有可能 的单态类型。

在泛型函数内部，如果使用了来自某个 trait 定义的行为，则需要为泛型指定相关的 trait 限定，来排除其他没有实现该trait 的类型。

【正例】

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

【反例】

```rust
use std::fmt;

// println! 中 `{:?}` 为 Debug triat 定义行为
fn some_func<T>(foo: T) {
    println!("{:?}", foo); // error[E0277]: `T` doesn't implement `Debug`
}
```
