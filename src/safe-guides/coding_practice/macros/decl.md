# 声明宏

[声明宏](https://doc.rust-lang.org/reference/macros-by-example.html) 也被叫做 示例宏（macros by example），或者简单地叫做 宏。目前声明宏使用 `macro_rules!`来定义。

声明宏的特点是：它只用作代码替换，而无法进行计算。

---
<!-- toc -->
---

## P.MAC.Decl.01 不要将声明宏内的变量作为外部变量使用

  【描述】

声明宏是半卫生（semi-hygienic）宏，其内部元变量（metavariables）不可作为外部变量去使用。

但是对于泛型参数（包括生命周期参数）是不卫生的，所以要小心使用。

【正例】

```rust
macro_rules! using_a {
    ($a:ident, $e:expr) => {{
        let $a = 42;
        $e
    }};
}
fn main() {
    let four = using_a!(a, a / 10);
}
```

【反例】

```rust
macro_rules! using_a {
    ($e:expr) => {
        {
            let a = 42;
            $e
        }
    }
}

let four = using_a!(a / 10); // build error:  cannot find value `a` in this scope
```

## P.MAC.Decl.02  在编写多个宏规则时，应该先从匹配粒度最小的开始写

  【描述】

因为 声明宏 中，是按规则的编写顺序来匹配的。当第一个规则被匹配到，后面的规则将永远不会匹配到。所以，编写声明宏规则时，需要先写匹配粒度最小的，最具体的规则，然后逐步编写匹配范围更广泛的规则。

【正例】

```rust
#[macro_export]
macro_rules! foo {
    (@as_expr $e:expr) => {$e}; // expr 比 tt 匹配更加具体

    ($($tts:tt)*) => {
        foo!(@as_expr $($tts)*)
    };
}
```

## P.MAC.Decl.03    不要在片段分类符（[fragment-specifier](https://doc.rust-lang.org/nightly/reference/macros-by-example.html#metavariables)）跟随它不匹配的符号

  【描述】

`macro_rules!` 定义声明宏时，非终止的元变量匹配必须紧随一个已被决定可以在这种匹配之后安全使用的标记。

具体的规则参见：  [Follow-set Ambiguity Restrictions](https://doc.rust-lang.org/reference/macros-by-example.html#follow-set-ambiguity-restrictions)

【正例】

该示例中，元变量`$e1`的 片段分类符`expr` 是非终止的，所以后面需要跟随一个用于分隔的标记。

Rust 规定在 `expr` 片段分类符 后面可以合法地跟随 `=>` / `,` / `;` 。

```rust
#[macro_export]
macro_rules! foo {
    ( $e1:expr, $e2:expr) => {$e1; $e2}; 
}

```

【  反例】

对于 `[,]` 这样的分隔标记就是非法的。这是为了防止未来 Rust 语法变动导致宏定义失效。

```rust
#[macro_export]
macro_rules! foo {
    ( $e1:expr [,] $e2:expr) => {$e1; $e2}; 
}

```

## P.MAC.Decl.04    匹配规则要精准，不要模糊不清

  【描述】

匹配规则必须精准，因为宏解析器并不会去执行代码，它无法匹配模糊不清的规则。

【正例】

```rust
macro_rules! ambiguity {
    ($i2:ident $($i:ident)* ) => { };
}

// ok
fn main() { ambiguity!(an_identifier  an_identifier2); }

```

【  反例】

宏解析器无法确定第一次匹配的应该是多少个 ident。

```rust
macro_rules! ambiguity {
    ($($i:ident)* $i2:ident) => { };
}

// error:
//    local ambiguity: multiple parsing options: built-in NTs ident ('i') or ident ('i2').
fn main() { ambiguity!(an_identifier); }
```

## P.MAC.Decl.05    使用宏替换（substitution）元变量的时候要注意选择合适的片段分类符（[fragment-specifier](https://doc.rust-lang.org/nightly/reference/macros-by-example.html#metavariables)）

  【描述】

 使用宏替换（substitution）元变量，就是指把已经进行过宏解析的 token 再次传给宏，需要注意，此时传入的 token，已经被看作是宏解析器解析后的 AST 节点了。

【正例】

满足示例这类正常匹配情况的目前只有 `tt`、`ident` 或者 `lifetime` 分类符。

```rust
macro_rules! capture_then_what_is {
    (#[$m:tt]) => {what_is!(#[$m])}; // 这里片段分类符用的是 tt
}

macro_rules! what_is {
    (#[no_mangle]) => {"no_mangle attribute"};
    (#[inline]) => {"inline attribute"};
    ($($tts:tt)*) => {concat!("something else (", stringify!($($tts)*), ")")};
}

fn main() {
    println!(
        "{}\n{}\n{}\n{}",
        what_is!(#[no_mangle]),
        what_is!(#[inline]),
        capture_then_what_is!(#[no_mangle]), // 被 capture_then_what_is 宏 解析过的token，还会被 what_is 二次处理
        capture_then_what_is!(#[inline]), // 被 capture_then_what_is 宏 解析过的token，还会被 what_is 二次处理
    );
}

// 输出：
// no_mangle attribute
// inline attribute
// no_mangle attribute
// inline attribute

```

【  反例】

```rust
macro_rules! capture_then_what_is {
    (#[$m:meta]) => {what_is!(#[$m])};   // 这里片段分类符用的是 meta
}

macro_rules! what_is {
    (#[no_mangle]) => {"no_mangle attribute"};
    (#[inline]) => {"inline attribute"};
    ($($tts:tt)*) => {concat!("something else (", stringify!($($tts)*), ")")};
}

fn main() {
    println!(
        "{}\n{}\n{}\n{}",
        what_is!(#[no_mangle]),
        what_is!(#[inline]),
        capture_then_what_is!(#[no_mangle]), // 被 capture_then_what_is 宏 解析过的token，不会再二次被 what_is 宏解析，所以按 tt 规则处理
        capture_then_what_is!(#[inline]), // 被 capture_then_what_is 宏 解析过的token，不会再二次被 what_is 宏解析，所以按 tt 规则处理
    );
}
// 输出：
// no_mangle attribute
// inline attribute
// something else (#[no_mangle])
// something else (#[inline])
```

## P.MAC.Decl.06   当宏需要接收 `self`时需要注意

  【描述】

 `self` 在 Rust 中属于关键字，它会在代码运行时被替换为具体类型的实例。当它传递给 宏 时，它会被看做一个变量，而宏对于变量而言，是具备卫生性的。而且，声明宏的作用只是替换，而非计算，它并不能计算出 self 的具体类型。

【正例】

```rust
macro_rules! double_method {
    ($self_:ident, $body:expr) => {
        fn double(mut $self_) -> Dummy {
            $body
        }
    };
}

struct Dummy(i32);

impl Dummy {
    double_method! {self, {
        self.0 *= 2;
        self
    }}
}

fn main() {
    println!("{:?}", Dummy(4).double().0);
}
```

【反例】

```rust
macro_rules! make_mutable {
    ($i:ident) => {let mut $i = $i;};
}

struct Dummy(i32);

impl Dummy {
    fn double(self) -> Dummy {
        make_mutable!(self);  // 这里传入的 self 和宏内部 let 定义的 self 不是一码事
        self.0 *= 2;
        self
    }
}

fn main() {
    println!("{:?}", Dummy(4).double().0);
}

```



## P.MAC.Decl.07   确保在宏定义之后再去调用宏

  【描述】

 Rust 中类型或函数，你可以在定义前后都可以调用它，但是宏不一样。 Rust  查找宏定义是按词法依赖顺序的，必须注意定义和调用的先后顺序。

【正例】

```rust
macro_rules! X { () => {}; }
mod a {
    X!(); // defined
}
mod b {
    X!(); // defined
}
mod c {
    X!(); // defined
}
fn main() {}
```

【反例】

```rust
mod a {
    // X!(); // undefined
}
mod b {
    // X!(); // undefined
    macro_rules! X { () => {}; }
    X!(); // defined
}
mod c {
    // X!(); // undefined
}
fn main() {}
```

【例外】

宏与宏之间相互调用，不受词法顺序的限制。

```rust
mod a {
    // X!(); // undefined
}

macro_rules! X { () => { Y!(); }; } // 注意：这里的 Y! 宏是在定义前被调用的，代码正常执行

mod b {
    // X!(); // defined, but Y! is undefined 
}

macro_rules! Y { () => {}; } // Y! 宏被定义在 X! 宏后面

mod c {
    X!(); // defined, and so is Y!
}
fn main() {}
```

## P.MAC.Decl.08  同一个 crate 内定义的宏相互调用时，需要注意卫生性

  【描述】

 当同一个 crate 内定义的宏相互调用时候，应该使用 `$crate` 元变量来指代当前被调用宏的路径。

【正例】

```rust
#[macro_export]
macro_rules! helped {
    () => { $crate::helper!() }
}

#[macro_export]
macro_rules! helper {
    () => { () }
}

//// 在另外的 crate 中使用这两个宏
// 注意：`helper_macro::helper` 并没有导入进来
use helper_macro::helped;

fn unit() {
   // OK! 这个宏能运行通过，因为 `$crate` 正确地展开成 `helper_macro` crate 的路径（而不是使用者的路径）
   helped!();
}
```

【反例】

```rust
#[macro_export]
macro_rules! helped {
    () => { helper!() } // This might lead to an error due to 'helper' not being in scope.
}

#[macro_export]
macro_rules! helper {
    () => { () }
}

//// 在另外的 crate 中使用这两个宏
// 注意：`helper_macro::helper` 并没有导入进来
use helper_macro::helped;

fn unit() {
   // Error! 这个宏会出现问题，因为其内部调用的 helper 宏的路径会被编译器认为是当前调用crate 的路径
   helped!();
}
```

