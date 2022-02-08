# Rust 编程技巧


### 设计模式之构建者模式

当需要很多构造函数，或构造含有很多可选配置项时，宜使用构建者模式

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

## 善用标准库中提供的迭代器适配器方法来满足自己的需求


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


## 可以使用`Cow<str>`来代替直接使用字符串，它可以减少Copy

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

## 错误处理：根据应用还是库来选择不同的错误处理方式

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

## 嵌入式（no-std）: 将一些公用的类型、函数、宏等集中到一个自定义的 `baremetal-std`

**【描述】**

虽然 `no-std` 下不能用Rust 的标准库，但是可以自定义 `no-std` 下的标准库 `baremetal-std`，用于积累 `no-std` 下常用的公共库。