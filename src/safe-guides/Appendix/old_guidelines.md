# K.淘汰的规则

--- 


## 常量

### G.CNS.03 不宜将量大的数据结构定义为常量

**淘汰原因**

虽然常量本质上是会内联，但Rust 支持类似于复制消除（Copy Elision）的优化（非强制），而且在不断改进中，对于这种大的数据应该会有相关优化。这里建议用静态变量来代替常量，也许会引入使用的复杂性，所以这条规则被淘汰。只保留对固定长度数组相关的规则。

相应修改：[G.TYP.ARR.01] 的描述也有相对修改。

**【级别】** 建议

**【描述】**

因为[常量会到处内联](https://doc.rust-lang.org/reference/items/constant-items.html#constant-items)，即复制到各个使用到它的地方。而静态变量不会内联，它是全局的且有一个引用地址。
所以当要创建一个很大的常量数组时，应该考虑将其换成静态变量以提高程序运行效率。（详情可见：[const-vs-static](https://rust-lang.github.io/rfcs/0246-const-vs-static.html#motivation)）

相关：[G.TYP.Array.01 ]

**【反例】**

```rust
fn main() {
    const MONTHS: [&str; 12] = ["January", "Feburary", "March", "April",
                                "May", "June", "July", "August",
                                "September", "October", "November", "December"];
}
```

**【正例】**

```rust
fn main() {
    static MONTHS: [&str; 12] = ["January", "Feburary", "March", "April",
                                "May", "June", "July", "August",
                                "September", "October", "November", "December"];
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| _ | no           | no           | _   | yes |

**【定制化参考】**

这条规则如果需要定制Lint，则需要找出每个定义的常量再判断其空间占用，或可直接排除基础类型以外的数据类型。

## 变量

### P.VAR.02  禁止将局部变量的引用返回函数外

**淘汰原因**

Rust 编译器可以检测到这种情况，之前考虑到编译器错误比较晦涩，列出该规则，但是进一步考虑到这个应该是 Rust 开发者最基本的认知，顾淘汰此规则。

**【描述】**

局部变量生命周期始于其声明终于其作用域结束。如果在其生命周期之外被引用，则程序的行为是未定义的。当然，Rust 编译器也会阻止你这么干。

*注： Rust 编译器可以检测到这种情况，但是编译器错误比较晦涩，本原则用来提示开发者注意这种情况。 *

**【反例】**

```rust
fn makestr() -> &String {
    let a = "test".to_string();
    &a
}

pub fn main() {
    let a = makestr();
}
```

### G.VAR.01  交换两个变量的值应使用 `swap` 而非赋值

**淘汰原因**

该条规则属于教程向，这应该是 Rust 开发者的基本认知。也不应该引导开发者使用swap。

**【级别】** 建议

**【描述】**

对于包含 `swap` 方法的类型，如 `ptr`、`slice`、`Cell`、`RefCell`、`VecDeque` 等建议使用该类型的 `swap` 方法进行交换。

对其他类型可以使用函数 `std::mem::swap` 进行变量值的交换。

**【反例】**

```rust
let mut a = 1;
let mut b = 2;
let mut c = 0; // 辅助交换的变量
c = a;
a = b;
b = c;  
```

**【正例】**

```rust
let mut a = 1;
let mut b = 2;
std::mem::swap(&mut a, &mut b);
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| _ | no           | no           | _   | yes |

**【定制化参考】**

这条规则如果需要定制Lint，则可以检测变量赋值操作，识别交换语义，推荐用户使用 `swap` 函数。

## 数据类型

### P.TYP.01 类型转换要尽量使用安全的方式

**淘汰原因**

和 G.TYP.01 有点重复。

**【描述】**

Rust 中的类型转换有多种方式，其中 `as` 强转、Unsafe 的 `std::mem::transmute` 为不安全转换。`From/Into`安全转换函数为安全转换。在使用类型转换时，应优先使用安全转换方式。

**【反例】**
```rust
// 产生数据截断
let a = i32::MAX;
let b = a as u16; 
println!("{}, {}", a, b); // 2147483647, 65535

// 产生精度损失
let a = std::f64::consts::PI;
let b = a as f32; 
println!("{}, {}", a, b); // 3.141592653589793, 3.1415927

// 结果不正确
let a: f64 = 123456.0;
let b: i64 = unsafe {
std::mem::transmute(a)
};
println!("{}, {}", a, b); // 123456, 4683220244930494464
```

**【正例】**
```rust
let a: f32 = 123456.0; 
let b: f64 = a.try_into().expect("trans failed"); 
println!("{}, {}", a, b); // 123456, 123456
```

### P.TYP.02  对数组和集合容器进行索引要使用 `usize` 类型

**淘汰原因**

这属于 Rust 开发者必备基本认知，有点偏教程向，故淘汰。

**【描述】**

Rust 中只允许索引为 `usize` 类型，因为：

1. 负索引是无意义的。
2. `usize`和 裸指针大小相同，意味着指针算法不会有任何隐藏的强制转换
3. `std::mem::size_of()` 和 `std::mem::align_of()` 的函数返回 `usize` 类型。
4. `usize` 不会因为平台架构的切换而导致索引值被截断的问题，比如 将`u32`类型的索引 用到 16位大小的嵌入式平台就会出问题。

### G.TYP.UNT.01   当函数不关心返回值但要处理错误时应使用单元类型

**淘汰原因**

这属于 Rust 开发者必备基本认知，有点偏教程向，故淘汰。

**【级别】** 建议

【定制化参考】

可以检测使用 `Option<T>` 包含 `Error` 类型的情况，发出警告。

**【描述】**

单元类型代表 `无返回值`。当返回类型无返回值但要处理错误时，应使用Result<(), Error>类型，

而非Option类型。

**【反例】**

```rust
fn f() -> Option<Error> {
    
    // ...
    
    None
}
```

**【正例】**

```rust
// 表示该函数要么成功，要么返回各自错误
fn f() -> Result<(), Error> {
    
    // ...
    
    // Error handle
    Ok(())
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| _ | no           | no           | _   | yes |



### G.TYP.INT.02 对于大整数字面量宜使用十六进制表示

**淘汰原因**

这个不做限制了，因人而异。

**【级别】** 建议

**【描述】**

略

**【反例】**

```rust
let a = `255` 
let b = `65_535`
let c =`4_042_322_160` 
```

**【正例】**

```rust
let a = `0xFF`
let b = `0xFFFF`
let c = `0xF0F0_F0F0
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [decimal_literal_representation](https://rust-lang.github.io/rust-clippy/master/#decimal_literal_representation) | yes           | no           | restriction | allow |




### P.TYP.REF.01  使用引用的时候要注意其生命周期不要重合

**淘汰原因**

偏教程向，这应该是 Rust 开发者基本认知，而且编译器会报错。

**【描述】**

在使用 引用的时候，要注意分析其生命周期，不可变借用和可变借用之间，以及可变借用之间不要有重叠。

**【反例】**

```rust
fn main(){
    let mut s = String::from("hello");
    // r1 是不可变借用，其生命周期和 可变借用 r3 重叠，所以会出问题
    let r1 = &s; // no problem    ---------------- lifetime r1 start
    let r2 = &mut s; // no problem
    let r3 = &mut s; // BIG PROBLEM -------------- lifetime r3 start 

    println!("{}, {}, and {}", r1, r2, r3);  //  lifetime r1, r2, r3  end; 
    
}
```

**【正例】**

```rust
fn main(){
    let mut s = String::from("hello");

    let r1 = &s; // no problem
    let r2 = &mut s; // no problem
    let r3 = &mut s; // no PROBLEM

    // println!("{}, {}, and {}", r1, r2, r3);
    
}
```


### P.TYP.TUP.01  宜使用元组解构来同时定义多个变量

**淘汰原因**

偏教程向，这应该是 Rust 开发者基本认知。

**【描述】**

可以利用元组解构的特性，来更好地精简代码。

**【示例】**

```rust
struct A(i32, i32);

fn hello( A(a, b): A){
    println!("{}, {}", a, b);
}

fn main(){
    let a = A(1, 2) ;
    hello(a);
}
```

### P.TYP.ARR.01 当数组长度在编译期就已经确定，应优先使用固定长度数组，而非动态数组（ `Vec<T>`）

**淘汰原因**

偏教程向，这应该是 Rust 开发者基本认知。

**【描述】**

固定长度数组会根据元素类型，优先选择存储在栈上，可以优化内存分配。当然，过大的数组可以酌情考虑放到堆内存，这个依据具体场景来决定。

当编译期长度可以确定，但长度并不是唯一确定的，那么可以考虑使用常量泛型。注意：常量泛型特性从 Rust 1.51版稳定。

**【示例】**

```rust
pub struct Grid {
    array: [u32; 5],
    width: usize,
    height: usize,
}
```

常量泛型：

```rust
pub struct Grid<T, const W: usize, const H: usize>
where
{
    array: [[T; W]; H],
}

impl<T, const W: usize, const H: usize> Default for Grid<T, W, H>
where
    T: Default + Copy,
{
    fn default() -> Self {
        Self {
            array: [[T::default(); W ]; H],
        }
    }
}

const WIDTH: usize = 300;
const HEIGHT: usize = 200;

fn main(){
    let _g = Grid::<usize, 3, 4>::default();  
    let _h = Grid::<usize, WIDTH, HEIGHT>::default();
}
```

注意，常量泛型目前还有一些特性并未完善，比如下面示例中的 `#![feature(generic_const_exprs)]` 特性，需要在 Nightly Rust 下使用。

```rust
#![feature(generic_const_exprs)]
pub struct Grid<T, const W: usize, const H: usize>
where
    [(); W * H]: Sized,
{
    array: [T; W * H],
}

impl<T, const W: usize, const H: usize> Default for Grid<T, W, H>
where
    [(); W * H]: Sized,
    T: Default + Copy,
{
    fn default() -> Self {
        Self {
            array: [T::default(); W * H],
        }
    }
}

const WIDTH: usize = 300;
const HEIGHT: usize = 200;

fn main(){
    let _g = Grid::<usize, 3, 4>::default();  
    let _h = Grid::<usize, WIDTH, HEIGHT>::default();
}

```

### P.TYP.SCT.02  当需要很多构造函数，或构造含有很多可选配置项时，宜使用构建者模式

**淘汰原因**

这条属于编程最佳实践，放到规范中有点臃肿，独立到最佳实践中。

**【描述】**

Rust 中没有默认的构造函数，都是自定义构造函数。

如果需要多个构造函数，或者构造时需要很多可选配置的复杂场景，那么构建者模式是适合你的选择。

**【示例】**

```rust
#[derive(Debug, PartialEq)]
pub struct Foo {
    // Lots of complicated fields.
    bar: String,
}

impl Foo {
    // This method will help users to discover the builder
    pub fn builder() -> FooBuilder {
        FooBuilder::default()
    }
}

#[derive(Default)]
pub struct FooBuilder {
    // Probably lots of optional fields.
    bar: String,
}

impl FooBuilder {
    pub fn new(/* ... */) -> FooBuilder {
        // Set the minimally required fields of Foo.
        FooBuilder {
            bar: String::from("X"),
        }
    }

    pub fn name(mut self, bar: String) -> FooBuilder {
        // Set the name on the builder itself, and return the builder by value.
        self.bar = bar;
        self
    }

    // If we can get away with not consuming the Builder here, that is an
    // advantage. It means we can use the FooBuilder as a template for constructing
    // many Foos.
    pub fn build(self) -> Foo {
        // Create a Foo from the FooBuilder, applying all settings in FooBuilder
        // to Foo.
        Foo { bar: self.bar }
    }
}

#[test]
fn builder_test() {
    let foo = Foo {
        bar: String::from("Y"),
    };
    let foo_from_builder: Foo = FooBuilder::new().name(String::from("Y")).build();
    assert_eq!(foo, foo_from_builder);
}
```


## P.TYP.ENM.01 需要取出 Enum 值的时候宜使用 `std::mem::take/swap/replace`

**淘汰原因**

偏教程向。

**【描述】**

需要取出 Enum 中值的时候，可能会遇到所有权的限制，此时可以使用 `std::men::take` 获取当前的值，而将默认值替换原值，这样可以避免所有权的限制。

但是 `std::men::take` 只适合实现 `Default` 的类型，这样就有默认实现可以替换了。

如果没有实现 `Default` 的类型，可以使用 `std::men::swap` 或 `std::mem::replace` 用给定的值来替换原值。

**【正例】**

```rust
use std::mem;

enum MultiVariateEnum {
    A { name: String },
    B { name: String },
    C,
    D
}

fn swizzle(e: &mut MultiVariateEnum) {
    use MultiVariateEnum::*;
    *e = match e {
        // Ownership rules do not allow taking `name` by value, but we cannot
        // take the value out of a mutable reference, unless we replace it:
        A { name } => B { name: mem::take(name) },
        B { name } => A { name: mem::take(name) },
        C => D,
        D => C
    }
}
```



## 表达式

### G.EXP.04  不应使用无效表达式语句

**淘汰原因**

属于代码逻辑问题，不应放到规范中。这类问题交给 Clippy 这类工具即可。


**【级别】** 建议

**【描述】**

无效的表达式语句，虽然会执行，但实际并没有起到什么效果。

也有例外情况存在。

**【反例】**

```rust
a+1;
```

**【正例】**

```rust
let a = 41;
let a = a+1;
```

**【例外】**

像在下面代码中，为了确保常量函数 `new` 可以在输入参数超出 MAX 限制的情况下 panic，使用了一个数组的技巧：` ["tag number out of range"][(byte > Self::MAX) as usize];` 。因为目前 在常量上下文中还无法直接使用 `panic!`，等 `const_panic` 功能稳定就可以了。

如果不加 `#[allow(clippy::no_effect)]` ，Clippy 会有警告。

```rust
// From: https://docs.rs/crate/der/0.4.1/source/src/tag/number.rs

#[derive(Copy, Clone, Debug, Eq, PartialEq, PartialOrd, Ord)]
pub struct TagNumber(pub(super) u8);

impl TagNumber {
    /// Maximum tag number supported (inclusive).
    pub const MAX: u8 = 30;

    /// Create a new tag number (const-friendly).
    ///
    /// Panics if the tag number is greater than [`TagNumber::MAX`]. For a fallible
    /// conversion, use [`TryFrom`] instead.
    #[allow(clippy::no_effect)]
    pub const fn new(byte: u8) -> Self {
        // TODO(tarcieri): hax! use const panic when available
        ["tag number out of range"][(byte > Self::MAX) as usize];
        Self(byte)
    }
	// ...   
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group     | level |
| ------------------------------------------------------------ | ------------- | ------------ | -------------- | ----- |
| [no_effect](https://rust-lang.github.io/rust-clippy/master/#no_effect) | yes           | no           | **complexity** | warn  |

## 控制流程

###  G.CTF.01 避免在流程控制分支中使用重复代码

**淘汰原因**

属于代码逻辑问题，不应放到规范中。这类问题交给 Clippy 这类工具即可。


**【级别】** 建议

**【描述】**

略

**【反例】**

```rust
let foo = if … {
    println!("Hello World");
    13
} else {
    println!("Hello World");
    42
};
```

**【正例】**

```rust
println!("Hello World");
let foo = if … {
    13
} else {
    42
};
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ |
| [branches_sharing_code](https://rust-lang.github.io/rust-clippy/master/#branches_sharing_code) | yes| no | nursery | allow |



### G.CTF.02 控制流程的分支逻辑要保持精炼


**淘汰原因**

属于代码逻辑问题，不应放到规范中。这类问题交给 Clippy 这类工具即可。


**【级别】** 建议

**【描述】**

略

**【反例】**

```rust

if x {
    …
} else {     // collapsible_else_if
    if y {
        …
    }
}

if x {  // collapsible_if
    if y {
        …
    }
}

// collapsible_match
fn func(opt: Option<Result<u64, String>>) {
    let n = match opt {
        Some(n) => match n {
            Ok(n) => n,
            _ => return,
        }
        None => return,
    };
}

// double_comparisons
# let x = 1;
# let y = 2;
if x == y || x < y {}

// wildcard_in_or_patterns    
match "foo" {
    "a" => {},
    "bar" | _ => {},
}

```

**【正例】**

```rust
// else if
if x {
    …
} else if y {
    …
}

// Merge multiple conditions
if x && y {
    …
}

// match 
fn func(opt: Option<Result<u64, String>>) {
    let n = match opt {
        Some(Ok(n)) => n,
        _ => return,
    };
}

// comparisons
# let x = 1;
# let y = 2;
if x <= y {}

// wildcard_in_or_patterns    
match "foo" {
    "a" => {},
    _ => {},
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group     | level |
| ------------------------------------------------------------ | ------------- | ------------ | -------------- | ----- |
| [collapsible_else_if](https://rust-lang.github.io/rust-clippy/master/#collapsible_else_if) | yes           | no           | style          | warn  |
| [collapsible_if](https://rust-lang.github.io/rust-clippy/master/#collapsible_if) | yes           | no           | style          | warn  |
| [collapsible_match](https://rust-lang.github.io/rust-clippy/master/#collapsible_match) | yes           | no           | style          | warn  |
| [double_comparisons](https://rust-lang.github.io/rust-clippy/master/#double_comparisons) | yes           | no           | **complexity** | warn  |
| [wildcard_in_or_patterns](https://rust-lang.github.io/rust-clippy/master/#wildcard_in_or_patterns) | yes           | no           | **complexity** | warn  |



### G.CTF.06 善用标准库中提供的迭代器适配器方法来满足自己的需求


**淘汰原因**

属于最佳实践编程技巧类。


**【级别】** 建议

**【描述】**

Rust 标准库中提供了很多迭代器方法，要学会使用它们，选择合适的方法来满足自己的需求。

下面示例中，反例中的迭代器适配器方法，都可以用对应的正例中的方法代替。

**【反例】**

```rust
// explicit_counter_loop
let v = vec![1];
fn bar(bar: usize, baz: usize) {}
let mut i = 0;
for item in &v {
    bar(i, *item);
    i += 1;
}

// filter_map_identity
let iter = vec![Some(1)].into_iter();
iter.filter_map(|x| x);

// filter_next
let vec = vec![1];
vec.iter().filter(|x| **x == 0).next();

// flat_map_identity
let iter = vec![vec![0]].into_iter();
iter.flat_map(|x| x);

// flat_map_option
let nums: Vec<i32> = ["1", "2", "whee!"].iter().flat_map(|x| x.parse().ok()).collect();
```

**【正例】**

```rust
// explicit_counter_loop
let v = vec![1];
fn bar(bar: usize, baz: usize) {}
for (i, item) in v.iter().enumerate() { bar(i, *item); }

// filter_map_identity
let iter = vec![Some(1)].into_iter();
iter.flatten();

// filter_next
let vec = vec![1];
vec.iter().find(|x| **x == 0);

// flat_map_identity
let iter = vec![vec![0]].into_iter();
iter.flatten();

// flat_map_option
let nums: Vec<i32> = ["1", "2", "whee!"].iter().filter_map(|x| x.parse().ok()).collect();

```
**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [explicit_counter_loop](https://rust-lang.github.io/rust-clippy/master/#explicit_counter_loop) | yes           | no           | complexity | warn  |
| [filter_map_identity](https://rust-lang.github.io/rust-clippy/master/#filter_map_identity) | yes           | no           | complexity | warn  |
| [filter_next](https://rust-lang.github.io/rust-clippy/master/#filter_next) | yes           | no           | complexity | warn  |
| [flat_map_identity](https://rust-lang.github.io/rust-clippy/master/#flat_map_identity) | yes           | no           | complexity | warn  |
| [flat_map_option](https://rust-lang.github.io/rust-clippy/master/#flat_map_option) | yes           | no           | pedantic | allow  |


## 字符串

### P.STR.03  可以使用`Cow<str>`来代替直接使用字符串，它可以减少Copy

**淘汰原因**

最佳实践编程技巧类。

**【描述】**

使用 `Cow<str>` 作为字符串处理函数参数和返回值，可以尽可能地减少数据Copy 和 内存分配。当字符串没有修改的时候，实际使用的是 `&'a str`，只有当数据修改的时候才会使用`String`。对于读操作大于写操作的场景，使用 `Cow<str>` 比较合适。

**【示例】**

```rust
// 对输入的字符串进行转义
pub fn naive<'a, S: Into<Cow<'a, str>>>(input: S) -> Cow<'a, str> {
    let input = input.into();
    fn is_trouble(c: char) -> bool {
        c == '<' || c == '>' || c == '&'
    }

    if input.contains(is_trouble) {
        let mut output = String::with_capacity(input.len());
        for c in input.chars() {
            match c {
                '<' => output.push_str("&lt;"),
                '>' => output.push_str("&gt;"),
                '&' => output.push_str("&amp;"),
                _ => output.push(c)
            }
        }
        // 只有在字符串修改的时候才使用 String
        Cow::Owned(output)
    } else {
        //其他情况使用 &str
        input
    }
}
```


## 集合容器

### P.CLT.01  根据集合各自的特点选择合适的集合类型

**淘汰原因**

文档教程向，不该放到规范中。

**【描述】**

Rust 标准库内置的集合类型，在安全和性能方面还是比较靠谱的。需要仔细阅读标准库中各类集合类型的优缺点来选择合适的类型。

**下列场景考虑 `Vec`**

-  你想要一个可动态增长大小（堆分配）的数组
- 你想要一个栈结构
- 你想要集合元素按特定顺序排序，并且仅需要在结尾追加新元素
- 你可能只是想临时收集一些元素，并且不关心它们的实际存储

**下列场景考虑 `VecDeque`**

- 你想要一个可以在头尾两端插入元素的 `Vec`
- 你想要一个队列，或双端队列

**下列场景考虑`LinkedList`**

- 你非常确定你真的需要一个双向链表

**下列场景考虑 `Hashmap`**

- 你需要一个 KV  集合
- 你想要一个缓存

**下列场景考虑 `BTreeMap`**

- 你需要一个可以排序的 `HashMap`
- 你希望可以按需获取一系列元素
- 你对最小或最大的 KV 感兴趣
- 你想要寻找比某个值更大或更小的键

**下列场景考虑使用 `Set` 系列**

- 你只是需要一个 Set 集合，而不需要键值对。

**下列场景考虑使用 `BinaryHeap`**

- 你想存储一堆元素，但只想在任何给定时间内处理 最大 或 最重要的元素
- 你想要一个优先队列

## 错误处理

### P.ERR.02  当函数的返回值或者结构体字段的值可能为空时，请使用`Option<T>`

**淘汰原因**

教程向，不该放到规范中。

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

### P.ERR.04   当程序中需要处理错误时，应该使用 `Result<T, E>` 和 `?` 操作符 

**淘汰原因**

教程向，不该放到规范中。


**【描述】**

当需要处理错误时，为了保证 程序的健壮性，应该尽可能处理错误。

**【反例】**

在实现原型类项目的时候，可以“快、糙、猛”地使用 `expect`  。但是要进生产环境，需要合理地处理错误。

```rust
let res: Result<usize, ()> = Ok(1);
res.expect("one"); // 如果有 Err， expect会 Panic ！

```

**【正例】**

```rust
let res: Result<usize, ()> = Ok(1);
res?;   // Ok::<(), ()>(())
```

### P.ERR.06   根据应用还是库来选择不同的错误处理方式

**淘汰原因**

最佳实践向，不该放到规范中。

**【描述】**

如果编写应用，建议使用` Error` trait对象；如果编写库，则建议返回自定义错误类型，方便下游处理


**【正例】**

```rust
// 对于应用使用 Error trait 对象更加方便
pub fn print(&self, languages: &Languages) -> Result<String, Box<dyn Error>> {
     // do something
}

// 对于库，暴露自定义错误类型更加方便下游处理错误
#[derive(Debug)]
pub struct SendError<T>(pub T);

impl<T> fmt::Display for SendError<T> {
    fn fmt(&self, fmt: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(fmt, "channel closed")
    }
}
```

### P.ERR.03   当程序中有不可恢复的错误时，应该让其 Panic

**淘汰原因**

教程向，不该放到规范中。

**【描述】**

如果遇到无法恢复的错误，则需要让程序 Panic。

相关 Clippy Lint： [if_then_panic](https://rust-lang.github.io/rust-clippy/master/#if_then_panic) 

**【正例】**

```rust
fn boot(ptr: *const usize) {
	if ptr.is_null() {
        panic!("ptr is null! boot failed!")
    }
    // or
    assert!(ptr.is_null(), "ptr is null! boot failed!");
}
```

## 多线程

### P.MTH.LOK.01 根据场景选择使用互斥锁还是 Channel 

**淘汰原因**

教程向，不该放到规范中。

**【描述】**

不要从哪种方式更快的角度来考虑，而应该从使用场景。性能取决于开发者如何使用它们。

一个简单的指南：

| **Channel** 适用于                                     | **Mutex** 适用于                |
| ------------------------------------------------------ | ------------------------------- |
| 传递数据所有权 <br /> 分发工作单元 <br /> 传递异步结果 | 修改共享缓存<br /> 修改共享状态 |

### G.MTH.LOK.02 多线程环境下宜使用 `Arc` 代替 `Rc`

**淘汰原因**

教程向，不该放到规范中。

**【级别】** 建议

**【描述】**

`Rc` 是专门用于单线程的，多线程下应该用 `Arc` 。

**【反例】**

```rust
use std::rc::Rc;
use std::sync::Mutex;
fn foo(interned: Rc<Mutex<i32>>) { ... }
```

**【正例】**

```rust
use std::rc::Rc;
use std::sync::Arc;
use std::cell::RefCell
fn foo(interned: Rc<RefCell<i32>>) { ... }
// or
fn foo(interned: Arc<Mutex<i32>>) { ... }
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [rc_mutex](https://rust-lang.github.io/rust-clippy/master/#rc_mutex) | yes           | no           | restriction | allow |


## no-std


### P.EMB.03  将一些公用的类型、函数、宏等集中到一个自定义的 `baremetal-std`

**淘汰原因**

最佳实践向，不该放到规范中。

**【描述】**

虽然 `no-std` 下不能用Rust 的标准库，但是可以自定义 `no-std` 下的标准库 `baremetal-std`，用于积累 `no-std` 下常用的公共库。

