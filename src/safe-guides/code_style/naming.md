# 命名

## P.01 标识符命名应该符合阅读习惯

> 标识符的命名要清晰、明了，有明确含义，容易理解。符合英文阅读习惯的命名将明显提高代码可读性。

一些好的实践包括但不限于：

- 使用正确的英文单词并符合英文语法，不要使用拼音
- 仅使用常见或领域内通用的单词缩写
- 布尔型变量或函数避免使用否定形式
- 尽量不要使用 Unicode 标识符。

## P.02 作用域越大，命名越精确；反之应简短

1. 对于全局函数、全局变量、宏、类型名、枚举命名，应当精确描述并全局唯一

```rust
static GET_COUNT: i32 = 42;  // 不符合：描述不精确
static GET_MAX_THREAD_COUNT: i32 = 42;  // 符合

// 如果必须这么长才能定义精准
enum VeryVerboseEnumOfThingsToDoWithNumbers {
    Add,
    Subtract,
}
// 那么可以配合使用类型别名来适当缩短命名，但要保持语义完整
type Operations = VeryVerboseEnumOfThingsToDoWithNumbers;

```

2. 对于函数局部变量，或者结构体、枚举中的成员变量，在其命名能够准确表达含义的前提下，应该尽量简短，避免冗余信息重复描述。

```rust

// 不符合

enum WebEvent {
    // An `enum` may either be `unit-like`,
    PageLoadEvent,
    PageUnloadEvent,
    // like tuple structs,
    KeyPressEvent(char),
    PasteEvent(String),
    // or c-like structures.
    ClickEvent { x: i64, y: i64 },
}



// 符合： 上下文信息已经知道它是 Evenet
enum WebEvent {
    // An `enum` may either be `unit-like`,
    PageLoad,
    PageUnload,
    // like tuple structs,
    KeyPress(char),
    Paste(String),
    // or c-like structures.
    Click { x: i64, y: i64 },
}


```

## G.NAM.01 使用统一的命名风格

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group |
| ------ | ---- | --------- | ------ | 
| _ | yes| no | Style |


Rust 命名规范在 [RFC 0430](https://github.com/rust-lang/rfcs/blob/master/text/0430-finalizing-naming-conventions.md) 中有描述。总的来说，Rust 倾向于在“类型级别”的结构中使用 `UpperCamelCase` 命名风格，在 “值（实例）级别”的结构中使用 `snake_case`命名风格。

下面是具体汇总。

| Item | 规范 |
| ---- | ---------- |
| 包（Crates） | [最好使用 snake_case](https://github.com/rust-lang/api-guidelines/issues/29) [^crate-name] |
| 模块（Modules） | `snake_case` |
| 类型（Types） | `UpperCamelCase` |
| 特质（Traits） | `UpperCamelCase` |
| 枚举体（Enum variants） | `UpperCamelCase` |
| 函数（Functions） | `snake_case` |
| 方法（Methods） | `snake_case` |
| 通用构造函数（General constructors） | `new` 或者 `with_more_details` |
| 转换构造函数（Conversion constructors） | `from_some_other_type` |
| 宏（Macros） | `snake_case!` |
| 本地变量（Local variables） | `snake_case` |
| 静态变量（Statics） | `SCREAMING_SNAKE_CASE` |
| 常量（Constants） | `SCREAMING_SNAKE_CASE` |
| 类型参数（Type parameters） | 简明的 `UpperCamelCase` ，通常使用单个大写字母： `T` |
| 生存期（Lifetimes） | 简短的 `lowercase`，通常使用单个小写字母 `'a`, `'de`, `'src`，尽量保持语义 |
| 特性（Features） | [有争议](https://github.com/rust-lang/api-guidelines/issues/101) ，但是一般遵照 [C-FEATURE] |

说明 :

1. 在 `UpperCamelCase`情况下，由首字母缩写组成的缩略语和 复合词的缩写，算作一个词。比如，应该使用 `Uuid` 而非 `UUID`，使用 `Usize` 而不是 `USize`，或者是 `Stdin` 而不是 `StdIn`。
2. 在`snake_case`中，首字母缩写和缩略词是小写的：is_xid_start。
3. 在 `snake_case` 或者 `SCREAMING_SNAKE_CASE` 情况下，每个词不应该由单个字母组成——除非这个字母是最后一个词。比如，使用 `btree_map` 而不使用 `b_tree_map`，使用 `PI_2` 而不使用 `PI2` 。


关于包命名：

- [^crate-name]: 由于历史问题，包名有两种形式 `snake_case` 或 `kebab-case` ，但实际在代码中需要引入包名的时候，Rust 只能识别 `snake_case`，也会自动将 `kebab-case` 识别为  `kebab_case`。
- Crate 的名称通常不应该使用 `-rs` 或者 `-rust` 作为后缀或者前缀。 因为每个 crate 都是 Rust 编写的！ 没必要一直提醒使用者这一点。但是有些情况下，比如是其他语言移植的同名 Rust 实现，则可以使用 `-rs` 后缀来表明这是 Rust 实现的版本。

## G.NAM.02 类型转换函数命名需要遵循所有权语义

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group |
| ------ | ---- | --------- | ------ | 
| wrong_self_convention| yes| no | Style |



应该使用带有以下前缀名称方法来进行特定类型转换：

| 名称前缀 | 内存代价 | 所有权 |
| ------ | ---- | --------- |
| `as_` | 无代价 | borrowed -\> borrowed |
| `to_` | 代价昂贵 | borrowed -\> borrowed<br>borrowed -\> owned (非 Copy 类型)<br>owned -\> owned (Copy 类型) |
| `into_` | 看情况 | owned -\> owned (非 Copy 类型) |

举例：

- `as_`
    - [`str::as_bytes()`] 
      用于查看 UTF-8 字节的 `str` 切片，
      这是无内存代价的（不会产生内存分配）。
      传入值是 `&str` 类型，输出值是 `&[u8]` 类型。
- `to_`
    - [`Path::to_str`] 
      对操作系统路径进行 UTF-8 字节检查，开销昂贵。
      虽然输入和输出都是借用，但是这个方法对运行时产生不容忽视的代价，
      所以不应使用 `as_str` 名称。
    - [`str::to_lowercase()`] 
      生成正确的 Unicode 小写字符，
      涉及遍历字符串的字符，可能需要分配内存。
      输入值是 `&str` 类型，输出值是 `String` 类型。
    - [`f64::to_radians()`] 
      把浮点数的角度制转换成弧度制。
      输入和输出都是 `f64` 。没必要传入 `&f64` ，因为复制 `f64` 花销很小。
      但是使用 `into_radians` 名称就会具有误导性，因为输入数据没有被消耗。
- `into_`
    - [`String::into_bytes()`]
      从 `String` 提取出背后的 `Vec<u8>` 数据，这是无代价的。
      它转移了 `String` 的所有权，然后返回具有所有权的 `Vec<u8>` 。
    - [`BufReader::into_inner()`] 
      转移了 buffered reader 的所有权，取出其背后的 reader ，这是无代价的。
      存于缓冲区的数据被丢弃了。
    - [`BufWriter::into_inner()`] 
      转移了 buffered writer 的所有权，取出其背后的 writer ，这可能以很大的代价刷新所有缓存数据。

[`str::as_bytes()`]: https://doc.rust-lang.org/std/primitive.str.html#method.as_bytes
[`Path::to_str`]: https://doc.rust-lang.org/std/path/struct.Path.html#method.to_str
[`str::to_lowercase()`]: https://doc.rust-lang.org/std/primitive.str.html#method.to_lowercase
[`f64::to_radians()`]: https://doc.rust-lang.org/std/primitive.f64.html#method.to_radians
[`String::into_bytes()`]: https://doc.rust-lang.org/std/string/struct.String.html#method.into_bytes
[`BufReader::into_inner()`]: https://doc.rust-lang.org/std/io/struct.BufReader.html#method.into_inner
[`BufWriter::into_inner()`]: https://doc.rust-lang.org/std/io/struct.BufWriter.html#method.into_inner

以 `as_` 和 `into_` 作为前缀的类型转换通常是 *降低抽象层次* ，要么是查看背后的数据 ( `as` ) ，要么是分解 (deconstructe) 背后的数据 ( `into` ) 。
相对来说，以 `to_` 作为前缀的类型转换处于同一个抽象层次，但是做了更多的工作。

当一个类型用更高级别的语义 (higher-level semantics) 封装 (wraps) 一个与之有关的值时，应该使用 `into_inner()` 方法名来取出被封装的值。

这适用于以下封装器：

读取缓存 ([`BufReader`]) 、编码或解码 ([`GzDecoder`]) 、取出原子 ([`AtomicBool`]) 、
或者任何相似的语义 ([`BufWriter`])。

[`BufReader`]: https://doc.rust-lang.org/std/io/struct.BufReader.html#method.into_inner
[`GzDecoder`]: https://docs.rs/flate2/1.0.20/flate2/read/struct.GzDecoder.html#method.into_inner
[`AtomicBool`]: https://doc.rust-lang.org/std/sync/atomic/struct.AtomicBool.html#method.into_inner
[`BufWriter`]: https://doc.rust-lang.org/std/io/struct.BufWriter.html#method.into_inner

如果类型转换方法返回的类型具有 `mut` 标识符，那么这个方法的名称应如同返回类型组成部分的顺序那样，带有 `mut` 名。
比如 [`Vec::as_mut_slice`] 返回 `mut slice` 类型，这个方法的功能正如其名称所述，所以这个名称优于 `as_slice_mut` 。

[`Vec::as_mut_slice`]: https://doc.rust-lang.org/std/vec/struct.Vec.html#method.as_mut_slice

```rust.ignored
// Return type is a mut slice.
fn as_mut_slice(&mut self) -> &mut [T];
```

更多来自标准库的例子：

- [`Result::as_ref`](https://doc.rust-lang.org/std/result/enum.Result.html#method.as_ref)
- [`RefCell::as_ptr`](https://doc.rust-lang.org/std/cell/struct.RefCell.html#method.as_ptr)
- [`slice::to_vec`](https://doc.rust-lang.org/std/primitive.slice.html#method.to_vec)
- [`Option::into_iter`](https://doc.rust-lang.org/std/option/enum.Option.html#method.into_iter)

## G.NAM.03 用于访问或获取数据的 `getter/setter` 类方法通常不要使用 `get_` 或 `set_` 等前缀

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group |是否可定制|
| ------ | ---- | --------- | ------ | ------ |
|  _ | no | no | _ | yes |

此规则 Clippy 不可检测，属于业务逻辑层面。

```rust
pub struct S {
    first: First,
    second: Second,
}

impl S {
    // 不建议 `get_first`。
    pub fn first(&self) -> &First {
        &self.first
    }

    // 不建议 `get_first_mut`, `get_mut_first`, or `mut_first`.
    pub fn first_mut(&mut self) -> &mut First {
        &mut self.first
    }
}
```

因为 Rust 所有权语义的存在，此例子中两个方法的参数分别是共享引用 `&self` 和 独占引用 `&mut self`，分别代表了 getter 和 setter 的语义。

但也存在例外情况：只有当有一个明显的东西可以通过`getter`得到时，才会使用`get`命名。例如，`Cell::get`可以访问一个`Cell`的内容。

对于进行运行时验证的getter，例如边界检查，可以考虑添加一个 Unsafe 的`_unchecked` 配套方法。一般来说，会有以下签名。

```rust
// 进行一些运行时验证，例如边界检查
fn get(&self, index: K) -> Option<&V>;
fn get_mut(&mut self, index: K) -> Option<&mut V>;
// 没有运行时验证，用于在某些情况下提升性能。比如，在当前运行环境中不可能发生越界的情况。
unsafe fn get_unchecked(&self, index: K) -> &V;
unsafe fn get_unchecked_mut(&mut self, index: K) -> &mut V;
```

getter 和类型转换 (G.NAM.02) 之间的区别很小，大部分时候不那么清晰可辨。比如 [`TempDir::path`] 可以被理解为临时目录的文件系统路径的 getter ，而 [`TempDir::into_path`] 负责把删除临时目录时转换的数据传给调用者。

因为 `path` 方法是一个 getter ，如果用 `get_path` 或者 `as_path` 就不对了。

[`TempDir::path`]: https://docs.rs/tempdir/0.3.7/tempdir/struct.TempDir.html#method.path
[`TempDir::into_path`]: https://docs.rs/tempdir/0.3.7/tempdir/struct.TempDir.html#method.into_path

来自标准库的例子：

- [`std::io::Cursor::get_mut`](https://doc.rust-lang.org/std/io/struct.Cursor.html#method.get_mut)
- [`std::ptr::Unique::get_mut`](https://doc.rust-lang.org/std/ptr/struct.Unique.html#method.get_mut)
- [`std::sync::PoisonError::get_mut`](https://doc.rust-lang.org/std/sync/struct.PoisonError.html#method.get_mut)
- [`std::sync::atomic::AtomicBool::get_mut`](https://doc.rust-lang.org/std/sync/atomic/struct.AtomicBool.html#method.get_mut)
- [`std::collections::hash_map::OccupiedEntry::get_mut`](https://doc.rust-lang.org/std/collections/hash_map/struct.OccupiedEntry.html#method.get_mut)
- [`<[T]>::get_unchecked`](https://doc.rust-lang.org/std/primitive.slice.html#method.get_unchecked)

## G.NAM.04 遵循 `iter/ iter_mut/ into_iter` 规范来生成迭代器

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group |是否可定制 |
| ------ | ---- | --------- | ------ | ------ |
|  _ | no | no | _ | yes |

此规则 Clippy 不可检测，属于业务逻辑层面。

参考 [RFC 199] 。

对于容纳 `U` 类型的容器 (container) ，其迭代器方法应该这样命名：

```rust,ignored
fn iter(&self) -> Iter             // Iter 实现 Iterator<Item = &U>
fn iter_mut(&mut self) -> IterMut  // IterMut 实现 Iterator<Item = &mut U>
fn into_iter(self) -> IntoIter     // IntoIter 实现 Iterator<Item = U>
```

此规则适用于在概念上属于同质集合的数据结构的方法，而非函数。例如，第三方库 `url`  中的 [percent_encode](https://docs.rs/url/1.4.0/url/percent_encoding/fn.percent_encode.html) 返回一个 URL 编码的字符串片段的迭代器。使用`iter/iter_mut/into_iter`约定的话，函数名就不会有任何明确的语义了。

标准库中存在一个反例： `str` 类型是有效 UTF-8 字节的切片（slice），概念上与同质集合略有差别，所以 `str` 没有提供 `iter`/`iter_mut`/`into_iter` 命名的迭代器方法，而是提供 [`str::bytes`] 方法来输出字节迭代器、[`str::chars`] 方法来输出字符迭代器。

[`str::bytes`]: https://doc.rust-lang.org/std/primitive.str.html#method.bytes
[`str::chars`]: https://doc.rust-lang.org/std/primitive.str.html#method.chars
[RFC 199]: https://github.com/rust-lang/rfcs/blob/master/text/0199-ownership-variants.md

来自标准库的例子：

- [`Vec::iter`](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.iter)
- [`Vec::iter_mut`](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.iter_mut)
- [`Vec::into_iter`](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.into_iter)
- [`BTreeMap::iter`](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html#method.iter)
- [`BTreeMap::iter_mut`](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html#method.iter_mut)

## G.NAM.05 迭代器类型名称应该与产生它们的方法相匹配


| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------ | ---- | --------- | ------ | ------ | 
|  _ | no | no | _ | yes |

此规则 Clippy 不可检测，属于业务逻辑层面。

> 一个叫做`into_iter()`的方法应该返回一个叫做`IntoIter`的类型，同样，所有其他返回迭代器的方法也是如此。

这条规则主要适用于方法，但通常对函数也有意义。例如，第三方库 `url`  中的 [percent_encode](https://docs.rs/url/1.4.0/url/percent_encoding/fn.percent_encode.html) 返回一个`PercentEncode` 类型的迭代器。


来自标准库的例子：

* [`Vec::iter`] 返回 [`Iter`][slice::Iter]
* [`Vec::iter_mut`] 返回 [`IterMut`][slice::IterMut]
* [`Vec::into_iter`] 返回 [`IntoIter`][vec::IntoIter]
* [`BTreeMap::keys`] 返回 [`Keys`][btree_map::Keys]
* [`BTreeMap::values`] 返回 [`Values`][btree_map::Values]

[`Vec::iter`]: https://doc.rust-lang.org/std/vec/struct.Vec.html#method.iter
[slice::Iter]: https://doc.rust-lang.org/std/slice/struct.Iter.html
[`Vec::iter_mut`]: https://doc.rust-lang.org/std/vec/struct.Vec.html#method.iter_mut
[slice::IterMut]: https://doc.rust-lang.org/std/slice/struct.IterMut.html
[`Vec::into_iter`]: https://doc.rust-lang.org/std/vec/struct.Vec.html#method.into_iter
[vec::IntoIter]: https://doc.rust-lang.org/std/vec/struct.IntoIter.html
[`BTreeMap::keys`]: https://doc.rust-lang.org/std/collections/struct.BTreeMap.html#method.keys
[btree_map::Keys]: https://doc.rust-lang.org/std/collections/btree_map/struct.Keys.html
[`BTreeMap::values`]: https://doc.rust-lang.org/std/collections/struct.BTreeMap.html#method.values
[btree_map::Values]: https://doc.rust-lang.org/std/collections/btree_map/struct.Values.html

## G.NAM.06  cargo feature 名中不应该含有无意义的占位词


| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------ | ---- | --------- | ------ | ------ |
|  _ | no | no | _ | no |

此规则 Clippy 不可检测，属于业务逻辑层面。

给 [Cargo feature] 命名时，不要带有无实际含义的的词语，比如无需 `use-abc` 或 `with-abc` ，而是直接以 `abc` 命名。

[Cargo feature]: http://doc.crates.io/manifest.html#the-features-section

这条原则经常出现在对 Rust 标准库进行 [可选依赖][optional-dependency] 配置的 crate 上。最简洁且正确的做法是：

```toml
# In Cargo.toml

[features]
default = ["std"]
std = []
```

```rust,ignored
// In lib.rs

#![cfg_attr(not(feature = "std"), no_std)]
```

这个例子中，不要给 feature 取 `use-std` 或者 `with-std` 或者除 `std` 之外另取名字。

feature 应与 Cargo 在推断可选依赖时隐含的 features 具有一致的名字。

假如 `x` crate 对 Serde 和 标准库具有可选依赖关系：

```toml
[package]
name = "x"
version = "0.1.0"

[features]
std = ["serde/std"]

[dependencies]
serde = { version = "1.0", optional = true }
```

当我们使用 `x` crate 时，可以使用 `features = ["serde"]` 开启 Serde 依赖。
类似地，也可以使用 `features = ["std"]` 开启标准库依赖。
Cargo 推断的隐含的 features 应该叫做 `serde` ，而不是 `use-serde` 或者 `with-serde` 。

与之相关的是， Cargo 要求 features 应该是叠加的，所以像 `no-abc` 这种负向的 feature 命名实际上并不正确。

[optional-dependency]:https://doc.rust-lang.org/cargo/reference/features.html#optional-dependencies


## G.NAM.07  名称应该使用统一的词序

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------ | ---- | --------- | ------ | ------ |
|  _ | no | no | _ | no |

此规则 Clippy 不可检测，属于业务逻辑层面。

以下是来自标准库的处理错误的一些类型：

- [`JoinPathsError`](https://doc.rust-lang.org/std/env/struct.JoinPathsError.html)
- [`ParseBoolError`](https://doc.rust-lang.org/std/str/struct.ParseBoolError.html)
- [`ParseCharError`](https://doc.rust-lang.org/std/char/struct.ParseCharError.html)
- [`ParseFloatError`](https://doc.rust-lang.org/std/num/struct.ParseFloatError.html)
- [`ParseIntError`](https://doc.rust-lang.org/std/num/struct.ParseIntError.html)
- [`RecvTimeoutError`](https://doc.rust-lang.org/std/sync/mpsc/enum.RecvTimeoutError.html)
- [`StripPrefixError`](https://doc.rust-lang.org/std/path/struct.StripPrefixError.html)


这些名称都按照 **动词-宾语-error** 的单词顺序。

如果增加“解析地址错误”类型，为了保持词性一致，应该使用 `ParseAddrError` 名称，而不是 `AddrParseError` 名称。 

具体选择什么样的词序并不重要，但务必要保证同一个 crate 内词序的一致性，以及与标准库相似函数的一致性。

