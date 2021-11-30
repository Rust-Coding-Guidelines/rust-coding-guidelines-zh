# 字符串

Rust 中字符串是有效的 UTF-8 编码的字节数组。

Rust 字符串类型众多，但本节内容主要围绕 ：`String` / `&str`  

---
<!-- toc -->
---

## P.STR.01   处理字符串非必要不要按字符来处理，应该按字节处理

**【描述】**

处理字符串有两种方式，一种是按字符处理，即把字符串转为字符数组`[char]`，另一种是直接按字节处理`[u8]`。

两者之间的一些区别：

- `[char]`  保证是有效的 Unicode，但不一定是有效的 UTF-8，一般将其看作是 UTF-32 。将字符数组转换为字符串需要注意。
- `[u8]` 不一定是有效的字符串，它比 `[char]` 节省内存。将其转换为字符串需要检查 `UTF-8`编码。

## P.STR.02   创建字符串时，可以预先分配大约足够的容量来避免后续操作中产生多次分配

**【描述】**

预分配足够的容量，避免后续内存分配，可以提升代码性能。

【正例】

```rust
let mut output = String::with_capacity(input.len());
```

【反例】

```rust
let mut output = String::new();
```

## P.STR.03    可以使用 `Cow<str>` 来代替直接使用字符串，它可以减少 Copy

**【描述】**

使用 `Cow<str>` 作为字符串处理函数参数和返回值，可以尽可能地减少数据Copy 和 内存分配。当字符串没有修改的时候，实际使用的是 `&'a str`，只有当数据修改的时候才会使用`String`。对于读操作大于写操作的场景，使用 `Cow<str>` 比较合适。

【正例】

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



## P.STR.04     在使用内建字符串处理函数或方法的时候，应该注意避免隐藏的嵌套迭代 或 多次迭代

**【描述】**

比如 `contains` 函数的实现就是按字符遍历字符串，但是如果你将它用于一个字符串的迭代处理中，就会产生嵌套迭代，时间复杂度从你以为的 `O(n)` 变成了 `O(n^2)`。没有将其用于迭代中，也有可能产生多次迭代，`O(n)` 变为 `O(n+m)` 。 为了避免这个问题，我们可以用 `find`  来代替 `contains`。

所以，在使用内建函数的时候要注意它的实现，选择合适的函数或方法，来避免这类问题。

【正例】

```rust
// 对输入的字符串进行转义
pub fn find<'a, S: Into<Cow<'a, str>>>(input: S) -> Cow<'a, str> {
    let input = input.into();
    fn is_trouble(c: char) -> bool {
        c == '<' || c == '>' || c == '&'
    }
    
    // 使用 find 而非 contains
    // find 使用模式查找，可以返回匹配字符的位置信息
    let first = input.find(is_trouble);
    
    // 利用 find 的位置信息，避免第二次遍历
    if let Some(first) = first {
        let mut output = String::from(&input[0..first]);
        output.reserve(input.len() - first);
        let rest = input[first..].chars();
        for c in rest {
            match c {
                '<' => output.push_str("&lt;"),
                '>' => output.push_str("&gt;"),
                '&' => output.push_str("&amp;"),
                _ => output.push(c),
            }
        }

        Cow::Owned(output)
    } else {
        input.into()
    }
}
```



## P.STR.05     只有在合适的场景下，才使用第三方库正则表达式`regex`  

**【描述】**

合适的场景包括：

1. 不在乎编译文件大小。`regex` 正则引擎是第三方库，引入它的时候意味着还会引入其他依赖，对编译文件大小有要求可以考虑，是否使用 `Cow` 和 内建函数方法来替代。
2. 对字符串查找性能有极致需求。`regex` 的  `find` 实现性能很好，但是 `replace` 替换就不一定了。对于替换需求，在适合 `Cow<str>` 的场景下，使用 `Cow` 和 内建函数方法来替代 regex 可能更好。

## P.STR.06    在拼接字符串时，建议使用 `format!` 

**【描述】**

在Rust中有很多方法可以连接字符串，不同的连接方法适用于不同的场景，性能也会有所差别。

【正例】

```rust
 // 组合字符串是最简单和直观的方法，尤其是在字符串和非字符串混合的情况下。
 fn main() {
	let name = "world!";
	let hw = format!("Hello {}!", name);
	println!("{:#?}", hw);
 }
 
 // 在追加字符串的时候，可以使用`push_str`方法,`push_str`性能比`format!`更好
 fn main() {
    let mut hw = String::new();
    hw.push_str("hello");
    hw.push_str(" world!");
    println!("{:#?}", hw);
}

 // 通过`concat()`方法将字符串数组拼接成一个字符串
 fn main() {
    let hw = ["hello", " ", "world!"].concat();
    println!("{:#?}", hw);
 }
 
 // 通过`join()`方法将字符串数组拼接成一个字符串
 fn main() {
    let hw_1 = ["hello", "world!"].join("");
    println!("{:#?}", hw_1);
	// 输出：
	// helloworld!
	
    // 使用`join()`方法在拼接字符串时添加或指定字符
    let hw_2 = ["hello", "world!"].join("+");
    println!("{:#?}", hw_2);
	// 输出：
	// hello+world!
 }
 
 // 使用`collect()`方式对数组中的字符串进行拼接
 fn main() {
    let hw = ["hello", " ", "world!"];
    let res: String = hw.iter().map(|x| *x).collect();
    println!("{:#?}", res);
 }
 
 // 使用符号`+`进行字符串拼接
 fn main() {
    let hw_1 = &(String::from("hello") + &String::from(" ") + &String::from("world!"));
    println!("{:#?}", hw_1);
    
    let hw_2 = &(String::from("hello") + " " + "world!");
    println!("{:#?}", hw_2);
    
    let hw_3 = &("hello".to_owned() + " " + "world!");
    println!("{:#?}", hw_3);

    let hw_4 = &("hello".to_string() + " " + "world!");
    println!("{:#?}", hw_4);
 }
```





---



## G.STR.01   在实现  `Display` trait 时不要调用 `to_string()` 方法

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [to_string_in_display](https://rust-lang.github.io/rust-clippy/master/#to_string_in_display) | yes           | no           | correctness | deny |

### 【描述】

因为 `to_string` 是间接通过 `Display` 来实现的，如果实现 `Display` 的时候再使用 `to_tring` 的话，将会无限递归。

【正例】

```rust
use std::fmt;

struct Structure(i32);
impl fmt::Display for Structure {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "{}", self.0)
    }
}
```

【反例】

```rust
use std::fmt;

struct Structure(i32);
impl fmt::Display for Structure {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "{}", self.to_string())
    }
}
```

## G.STR.02   在追加字符串时使用 `push_str`方法可读性更强

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [string_add_assign](https://rust-lang.github.io/rust-clippy/master/#string_add_assign) | yes           | no           | pedantic    | allow |
| [string_add](https://rust-lang.github.io/rust-clippy/master/#string_add) | yes           | no           | restriction | allow |

### 【描述】

【正例】

```rust
let mut x = "Hello".to_owned();

// More readable
x += ", World";
x.push_str(", World");
```

【反例】

```rust
let mut x = "Hello".to_owned();
x = x + ", World";
```



## G.STR.03    将只包含 `ASCII`字符的字符串字面量转为字节序列可以直接使用`b"str"` 语法代替调用`as_bytes`方法

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [string_lit_as_bytes](https://rust-lang.github.io/rust-clippy/master/#string_lit_as_bytes) | yes           | no           | nursery | allow |

### 【描述】

这是为了增强可读性，让代码更简洁。

注意，`"str".as_bytes()` 并不等价于  `b"str"`，而是等价于 `&b"str"[..]`  。

【正例】

```rust
let bs = b"a byte string";
```

【反例】

```rust
let bs = "a byte string".as_bytes();
```

## G.STR.04   需要判断字符串以哪个字符开头或结尾时，不要按字符迭代比较

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [chars_last_cmp](https://rust-lang.github.io/rust-clippy/master/#chars_last_cmp) | yes           | no           | style      | warn  |
| [chars_next_cmp](https://rust-lang.github.io/rust-clippy/master/#chars_next_cmp) | yes           | no           | style      | warn  |

### 【描述】

Rust 语言 核心库 和 标准库都对字符串内置了一些方便的方法来处理这类需求。

迭代字符的性能虽然也很快（对500多个字符迭代转义处理大概需要4.5微秒左右），但这种场景用迭代的话，代码可读性更差一些。

【正例】

```rust
let name = "_";
name.ends_with('_') || name.ends_with('-');

let name = "foo";
if name.starts_with('_') {};
```

 【反例】

```rust
let name = "_";
name.chars().last() == Some('_') || name.chars().next_back() == Some('-');

let name = "foo";
if name.chars().next() == Some('_') {};
```

## G.STR.05   对字符串按指定位置进行切片的时候需要小心破坏其 UTF-8 编码

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [string_slice](https://rust-lang.github.io/rust-clippy/master/#string_slice) | yes           | no           | restriction | allow |

### 【描述】

字符串默认是合法的 `UTF-8`字节序列，如果通过指定索引位置来对字符串进行切片，有可能破坏其合法 `UTF-8` 编码，除非这个位置是确定的，比如按 `char_indices` 方法来定位是合法的。

【正例】

```rust
let s = "Ölkanne";
let mut char_indices = s.char_indices();
assert_eq!(Some((0, 'Ö')), char_indices.next());
// assert_eq!(Some((2, 'l')), char_indices.next()); 
let pos = if let Some((pos, _)) = char_indices.next(){ pos } else {0};
let sub_s = &s[pos..];
assert_eq!("lkanne", sub_s);
```

【反例】

```rust
let s = "Ölkanne";
// thread 'main' panicked at 'byte index 1 is not a char boundary; 
// it is inside 'Ö' (bytes 0..2) of `Ölkanne`'
let sub_s = &s[1..];
// println!("{:?}", sub_s);
```

