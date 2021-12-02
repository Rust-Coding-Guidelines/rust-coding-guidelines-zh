# 结构体

Rust 包含了三种结构体： 命名结构体、元组结构体、单元结构体。

---
<!-- toc -->
---
## P.TYP.Struct.01 为结构体实现构造性方法时，避免构造后再初始化的情况

**【描述】**

跟其他OOP 或 FP 语言不一样， Rust 的惯用方式是构建即初始化。

**【反例】**

```rust
// 先构建
let mut dict = Dictionary::new();
// 后初始化
dict.load_from_file("./words.txt")?;
```

**【正例】**

```rust
// 构建即初始化
let dict = Dictionary::from_file("./words.txt")?;

impl Dictionary {
  fn from_file(filename: impl AsRef<Path>) -> Result<Self, Error> {
    let text = std::fs::read_to_string(filename)?;
    // 不会去存储空状态
    let mut words = Vec::new();
    for line in text.lines() {
      words.push(line);
    }
    Ok(Dictionary { words })
  }
}
```


## P.TYP.Struct.02  当需要很多构造函数，或构造含有很多可选配置项时，宜使用构建者模式

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

## P.TYP.Struct.03  结构体实例需要默认实现时，宜使用`Default`特征

**【描述】**

为结构体实现 `Default` 对于简化代码提高可读性很有帮助。

**【示例】**

 ```rust
 use std::{path::PathBuf, time::Duration};
 
 // note that we can simply auto-derive Default here.
 #[derive(Default, Debug, PartialEq)]
 struct MyConfiguration {
     // Option defaults to None
     output: Option<PathBuf>,
     // Vecs default to empty vector
     search_path: Vec<PathBuf>,
     // Duration defaults to zero time
     timeout: Duration,
     // bool defaults to false
     check: bool,
 }
 
 impl MyConfiguration {
     // add setters here
 }
 
 fn main() {
     // construct a new instance with default values
     let mut conf = MyConfiguration::default();
     // do something with conf here
     conf.check = true;
     println!("conf = {:#?}", conf);
         
     // partial initialization with default values, creates the same instance
     let conf1 = MyConfiguration {
         check: true,
         ..Default::default()
     };
     assert_eq!(conf, conf1);
 }
 ```


---

## G.TYP.Struct.01    对外导出的公开的 Struct，宜添加`#[non_exhaustive]`属性

**【级别】** 建议

**【描述】**

作为对外公开的 结构体，为了保持稳定性，应该使用 `#[non_exhaustive]`属性，避免因为将来结构体字段发生变化而影响到下游的使用。主要涉及命名结构体和元组结构体。

**【反例】**

在 `#[non_exhaustive]` 属性稳定之前，社区内还有一种约定俗成的写法来达到防止下游自定义枚举方法。通过 `manual_non_exhaustive` 可以监控这类写法。

```rust
struct S {
    pub a: i32,
    pub b: i32,
    _priv: (),  // 这里用 下划线作为前缀定义的字段，作为私有字段，不对外公开
}

// 用户无法自定义实现该结构体的方法。
```

**【正例】**

```rust
#[non_exhaustive]
struct Foo {
    bar: u8,
    baz: String,
}
```

**【例外】**

也有例外情况！

 从语义角度看，`#[non_exhaustive]` 只是代表未穷尽的字段或枚举变体，是为了表达“未来可能有变化”这种语义。

但是当要表达 “这个结构体不允许对方实例化” 的语义时，通过自定义的 `_priv`字段就可以更好地表达这个语义。

而使用 `#[non_exhaustive]` 虽然也能达到 “不允许对方实例化的目的”，但是在代码可读性层面，却无法表达出这个语义。

```rust
// From: https://github.com/tokio-rs/tokio/blob/master/tokio-util/src/codec/framed.rs

#[allow(clippy::manual_non_exhaustive)]
pub struct FramedParts<T, U> {
    pub io: T,
    pub codec: U,
    pub read_buf: BytesMut,
    pub write_buf: BytesMut,
    
    /// This private field allows us to add additional fields in the future in a
    /// backwards compatible way.
    _priv: (),
}

impl<T, U> FramedParts<T, U> {
    /// Create a new, default, `FramedParts`
    pub fn new<I>(io: T, codec: U) -> FramedParts<T, U>
    where
        U: Encoder<I>,
    {
        FramedParts {
            io,
            codec,
            read_buf: BytesMut::new(),
            write_buf: BytesMut::new(),
            _priv: (),
        }
    }
}
```

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [exhaustive_structs](https://rust-lang.github.io/rust-clippy/master/#exhaustive_structs) | yes           | no           | restriction | allow |
| [manual_non_exhaustive](https://rust-lang.github.io/rust-clippy/master/#manual_non_exhaustive) | yes           | no           | style       | warn  |


## G.TYP.Struct.02  当结构体中有超过三个布尔类型的字段，宜将其独立为一个枚举

**【级别】** 建议

**【描述】**

这样有助于提升 代码可读性和 API 。

**【反例】**

```rust
struct S {
    is_pending: bool,
    is_processing: bool,
    is_finished: bool,
}
```

**【正例】**

```rust
enum S {
    Pending,
    Processing,
    Finished,
}
```

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [struct_excessive_bools](https://rust-lang.github.io/rust-clippy/master/#struct_excessive_bools) | yes           | no           | pedantic   | allow |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 用于配置函数可以拥有的 bool 类型参数最大数量，默认为 3。
max-struct-bools=3 
```

## G.TYP.Struct.03  宜使用结构体功能更新语法来提升代码可读性

**【级别】** 建议

**【描述】**

略

**【反例】**

```rust
let mut a: A = Default::default();
a.i = 42;
```

**【正例】**

```rust
let a = A {
    i: 42,
    .. Default::default()
};
```

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [field_reassign_with_default](https://rust-lang.github.io/rust-clippy/master/#field_reassign_with_default) | yes           | no           | style      | warn  |


