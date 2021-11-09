# 格式

Rust 有自动化格式化工具 rustfmt ，可以帮助开发者摆脱手工调整代码格式的工作，提升生产力。但是，rustfmt 遵循什么样的风格规范，作为开发者应该需要了解，在编写代码的时候可以主动按这样的风格编写。

说明：以下 `rustfmt` 配置中对应配置项如果 `Stable`为`No`，则表示该配置项不能用于 Stable Rust 下在 `rustfmt.toml` 中自定义，但其默认值会在`cargo fmt`时生效。在 Nightly Rust 下则都可以自定义。

在 Stable Rust 下使用未稳定配置项的方法、了解配置示例及其他全局配置项说明请参阅：[Rustfmt 配置相关说明](../../tools/rustfmt.md) 。

注意： 以下规则 针对 rustfmt  version 1.4.36 版本。

---


## P.FMT.01 代码格式以保证可读性为前提

### 【描述】

制定统一的编码风格，是为了提升代码的可读性，让日常代码维护和团队之间审查代码更加方便。

---


## G.FMT.01 始终使用 rustfmt 进行自动格式化代码

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group |是否可定制|
| ------ | ---- | --------- | ------ | ------ |
|  _ | no | no | _ | yes |

【定制化建议】

通过检测 项目 根目录下是否存在 `rustfmt.toml` 或 `.rustfmt.toml` ，如果没有该文件，则发出警告，让开发者使用 rustfmt 来格式化代码。

### 【描述】

应该总是在项目中添加 `rustfmt.toml` 或 `.rustfmt.toml`文件，即使它是空文件。这是向潜在的合作者表明你希望代码是自动格式化的。

### 【例外】

在特殊的情况下，可以通过条件编译属性 `#[cfg_attr(rustfmt, rustfmt_skip)]` 或 `#[rustfmt::skip]` 来关闭自动格式化。

比如下面示例：

`vec!` 中的元素排布是固定格式，这样有助于开发的便利。

```rust
fn main() {
    let got = vec![
            0x00, 0x05, 0x01, 0x00,
            0xff,
            0x00,
            0x00,
    
            0x01, 0x0c, 0x02, 0x00,
            0xde, 0xad, 0xbe, 0xef, 0xde, 0xad, 0xbe, 0xef,
            b'd', b'e', b'a', b'd', b'b', b'e', b'e', b'f', 0x00,
            0x00,
    
            127, 0x06, 0x03, 0x00,
            0x01, 0x02,
            b'a', b'b', b'c', b'd', 0x00,
            b'1', b'2', b'3', b'4', 0x00,
            0x00,
    ];
}
```

如果使用 自动格式化，会变成：

```rust
fn main() {
    let got = vec![
        0x00, 0x05, 0x01, 0x00, 0xff, 0x00, 0x00, 0x01, 0x0c, 0x02, 0x00, 0xde, 0xad, 0xbe, 0xef,
        0xde, 0xad, 0xbe, 0xef, b'd', b'e', b'a', b'd', b'b', b'e', b'e', b'f', 0x00, 0x00, 127,
        0x06, 0x03, 0x00, 0x01, 0x02, b'a', b'b', b'c', b'd', 0x00, b'1', b'2', b'3', b'4', 0x00,
        0x00,
    ];
}
```

但是加上 `#[rustfmt::skip]` 就不会被自动格式化影响：

```rust
fn main() {
    #[rustfmt::skip] 
    let got = vec![
            0x00, 0x05, 0x01, 0x00,
            0xff,
            0x00,
            0x00,
    
            0x01, 0x0c, 0x02, 0x00,
            0xde, 0xad, 0xbe, 0xef, 0xde, 0xad, 0xbe, 0xef,
            b'd', b'e', b'a', b'd', b'b', b'e', b'e', b'f', 0x00,
            0x00,
    
            127, 0x06, 0x03, 0x00,
            0x01, 0x02,
            b'a', b'b', b'c', b'd', 0x00,
            b'1', b'2', b'3', b'4', 0x00,
            0x00,
    ];
}

```


## G.FMT.02 缩进始终使用空格（space）而非制表符（tab）

### 【级别：必须】

必须严格按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明|
| ------ | ---- | ---- | ---- | 
| [`tab_spaces`](https://rust-lang.github.io/rustfmt/#tab_spaces) | 4| yes（默认）| 缩进空格数｜
|[`hard_tabs`](https://rust-lang.github.io/rustfmt/#hard_tabs)| false| yes（默认）| 禁止使用tab缩进｜

### 【描述】

1. 缩进要使用 四个 空格，不要使用制表符（`\t`）代替。
2. 通过 IDE/Editor 为缩进默认好设置值。


## G.FMT.03 每行最大宽度为 100 个字符

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- |
| [`max_width`](https://rust-lang.github.io/rustfmt/?#max_width) | 100 | yes（默认）| 行最大宽度默认值|
|[`error_on_line_overflow`](https://rust-lang.github.io/rustfmt/?#error_on_line_overflow)| false（默认）| No (tracking issue: #3391)| 如果超过最大行宽设置则报错|
|[`use_small_heuristics`](https://rust-lang.github.io/rustfmt/?#use_small_heuristics)| Default（默认）Max（推荐） | Yes| 统一管理宽度设置|

### 【描述】

代码行宽不宜过长，否则不利于阅读。
建议每行字符数不要超过 100 个字符。

`rustfmt` 还提供很多其他宽度设置：

- fn_call_width, 函数调用最大宽度设置，其默认值是 `max_width`的 `60%`。
- attr_fn_like_width, 像函数那样使用的属性宏最大宽度，其默认值是 `max_width`的 `70%`。
- struct_lit_width,  结构体字面量最大宽度，其默认值是 `max_width`的 `18%`。
- struct_variant_width, 结构体变量最大宽度，其默认值是 `max_width`的 `35%`。
- array_width, 数组最大宽度，其默认值是 `max_width`的 `60%`。
- chain_width, 链式结构最大宽度，其默认值是 `max_width`的 `60%`。
- single_line_if_else_max_width，单行 `if-else` 最大宽度，其默认值是 `max_width`的 `50%`。

这么多宽度设置管理起来比较麻烦，所以使用 `use_small_heuristics` 来管理更好。

### 【示例】

`use_small_heuristics` 默认配置示例。

【正例】

```rust
enum Lorem {
    Ipsum,
    Dolor(bool),
    Sit { amet: Consectetur, adipiscing: Elit },
}

fn main() {
    lorem(
        "lorem",
        "ipsum",
        "dolor",
        "sit",
        "amet",
        "consectetur",
        "adipiscing",
    );

    let lorem = Lorem {
        ipsum: dolor,
        sit: amet,
    };
    let lorem = Lorem { ipsum: dolor };

    let lorem = if ipsum { dolor } else { sit };
}
```

【反例】

当`use_small_heuristics` 配置为 `Off` :

```rust
enum Lorem {
    Ipsum,
    Dolor(bool),
    Sit {
        amet: Consectetur,
        adipiscing: Elit,
    },
}

fn main() {
    lorem("lorem", "ipsum", "dolor", "sit", "amet", "consectetur", "adipiscing");

    let lorem = Lorem {
        ipsum: dolor,
        sit: amet,
    };

    let lorem = if ipsum {
        dolor
    } else {
        sit
    };
}
```

当`use_small_heuristics` 配置为 `Max` :

```rust
enum Lorem {
    Ipsum,
    Dolor(bool),
    Sit { amet: Consectetur, adipiscing: Elit },
}

fn main() {
    lorem("lorem", "ipsum", "dolor", "sit", "amet", "consectetur", "adipiscing");

    let lorem = Lorem { ipsum: dolor, sit: amet };

    let lorem = if ipsum { dolor } else { sit };
}
```

## G.FMT.04 行间距最大宽度空一行

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`blank_lines_lower_bound`](https://rust-lang.github.io/rustfmt/?#blank_lines_lower_bound) | 0（默认） | No| 不空行|
|[`blank_lines_upper_bound`](https://rust-lang.github.io/rustfmt/?#blank_lines_upper_bound)| 1（默认）| No | 最大空一行|

### 【描述】

代码行之间，最小间隔 `0` 行，最大间隔`1`行。

### 【示例】

【正例】

```rust
fn foo() {
    println!("a");
}
// 1
fn bar() {
    println!("b");
    println!("c");
}
```

或者

```rust
fn foo() {
    println!("a");
}
fn bar() {
    println!("b");
	// 1
    println!("c");
}

```

【反例】

```rust
fn foo() {
    println!("a");
}
// 1
// 2
fn bar() {
    println!("b");
// 1
// 2
    println!("c");
}
```

## G.FMT.05 语言项（Item) 定义时花括号（brace）位置应该与语言项保持同一行

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`brace_style`](https://rust-lang.github.io/rustfmt/?#brace_style) | SameLineWhere （默认）| No| 应该与语言项保持同一行，但是 where 语句例外 |
|[`brace_style`](https://rust-lang.github.io/rustfmt/?#brace_style)| AlwaysNextLine | No | 应该在语言项的下一行 |
|[`brace_style`](https://rust-lang.github.io/rustfmt/?#brace_style)| PreferSameLine | No | 总是优先与语言项保持同一行，where 语句也不例外 |
|[`where_single_line`](https://rust-lang.github.io/rustfmt/?#where_single_line)| false（默认）| No |  强制将 `where` 子句放在同一行上 |
|[`brace_style` in control-flow](https://rust-lang.github.io/rustfmt/?#AlwaysSameLine)| AlwaysSameLine （默认） | No |  总在同一行上，用于控制流程中默认值 |
|[[`brace_style` in control-flow](https://rust-lang.github.io/rustfmt/?#ClosingNextLine)| ClosingNextLine| No |  用于控制流程中 else 分支在 if 分支结尾处换行|


### 【描述】

花括号的位置风格默认使用 `SameLineWhere`，但是也根据不同的语言项略有区别。

### 【示例】

#### 函数 

【正例】

```rust
fn lorem() { // 花括号和fn定义在同一行
    // body
}

fn lorem(ipsum: usize) { // 花括号和fn定义在同一行
    // body
}

// 当有 `where` 子句的时候，花括号换行
// 并且，`where` 子句和 `where` 关键字不在同一行
fn lorem<T>(ipsum: T)
where
    T: Add + Sub + Mul + Div,
{
    // body
}
```

通过配置 `where_single_line` 为 true，方可设置 `where`子句在同一行，如下：

```rust
// 当有 `where` 子句的时候，花括号换行
// 设置了 `where_single_line=true` ，则`where` 子句和 `where` 关键字在同一行
fn lorem<T>(ipsum: T)
where T: Add + Sub + Mul + Div,
{
    // body
}
```

【反例】

如果设置 `brace_style = "AlwaysNextLine"`，则：

```rust
fn lorem()
{
    // body
}

fn lorem(ipsum: usize)
{
    // body
}

fn lorem<T>(ipsum: T)
where
    T: Add + Sub + Mul + Div,
{
    // body
}
```

如果设置 `brace_style = "PreferSameLine"`，则：

```rust
fn lorem() {
    // body
}

fn lorem(ipsum: usize) {
    // body
}

fn lorem<T>(ipsum: T)
where
    T: Add + Sub + Mul + Div, { // 注意这里和 `SameLineWhere`的区别
    // body
}
```

#### 结构体与枚举

【正例】

```rust
struct Lorem {
    ipsum: bool,
}

struct Dolor<T>
where
    T: Eq,
{
    sit: T,
}
```

【反例】

如果设置 `brace_style = "AlwaysNextLine"`，则：


```rust
struct Lorem
{
    ipsum: bool,
}

struct Dolor<T>
where
    T: Eq,
{
    sit: T,
}
```

如果设置 `brace_style = "PreferSameLine"`，则：

```rust
struct Lorem {
    ipsum: bool,
}

struct Dolor<T>
where
    T: Eq, {
    sit: T,
}
```

#### 流程控制

流程控制倾向于默认使用 `AlwaysSameLine`，即，总在同一行。因为流程控制没有`where`子句。

【正例】

```rust
// "AlwaysSameLine" (default)
fn main() {
    if lorem {
        println!("ipsum!");
    } else {
        println!("dolor!");
    }
}
```

【反例】

如果设置 `brace_style = "AlwaysNextLine"`，则：

```rust
fn main() {
    if lorem
    {
        println!("ipsum!");
    }
    else
    {
        println!("dolor!");
    }
}
```

如果设置 `brace_style = "ClosingNextLine"`，则：

```rust
fn main() {
    if lorem {
        println!("ipsum!");
    } // 注意这里 if 分支结尾处，else 换行
    else { 
        println!("dolor!");
    }
}
```


## G.FMT.06 单行规则

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 默认值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`empty_item_single_line`](https://rust-lang.github.io/rustfmt/?#empty_item_single_line) | true（默认） | No| 当语言项内容为空时，要保持单行 |
| [`fn_single_line`](https://rust-lang.github.io/rustfmt/?#empty_item_single_line) | false（默认） | No| 当函数中只有一个表达式时不要保持单行 |
| [`struct_lit_single_line`](https://rust-lang.github.io/rustfmt/?#struct_lit_single_line) | true（默认） | No| 当函数中只有一个表达式时不要保持单行 |


### 【描述】

当语言项内容为空时，即空函数，空结构体，空实现等，要保持单独一行。但是，当函数中只有一个表达式时，请不要保持单行。

### 【示例】

【正例】

```rust
fn lorem() {}

impl Lorem {}

fn lorem() -> usize {
    42
}

fn main() {
    let lorem = Lorem { foo: bar, baz: ofo };
}

```

【反例】

```rust
fn lorem() {
}

impl Lorem {
}

fn lorem() -> usize { 42 }

fn main() {
    let lorem = Lorem {
        foo: bar,
        baz: ofo,
    };
}
```

## G.FMT.07 存在多个标识符时应该保持块状（Block）缩进

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`indent_style`](https://rust-lang.github.io/rustfmt/?#indent_style) | Block（默认） | No| 多个标识符定义保持块状风格，但看上去可能不太工整 |
| [`indent_style`](https://rust-lang.github.io/rustfmt/?#indent_style) | Visual | No| 多个标识符定义保持对齐风格，为了看上去工整 |

### 【描述】

当在表达式或语言项定义中出现多个标识符，则应该让其保持块状风格缩进。

### 【示例】

#### 数组

【正例】

```rust
fn main() {
    let lorem = vec![
        "ipsum",
        "dolor",
        "sit",
        "amet",
        "consectetur",
        "adipiscing",
        "elit",
    ];
}
```

【反例】

```rust
fn main() {
    let lorem = vec!["ipsum",
                     "dolor",
                     "sit",
                     "amet",
                     "consectetur",
                     "adipiscing",
                     "elit"];
}
```

#### 流程控制

【正例】

```rust
fn main() {
    if lorem_ipsum
        && dolor_sit
        && amet_consectetur
        && lorem_sit
        && dolor_consectetur
        && amet_ipsum
        && lorem_consectetur
    {
        // ...
    }
}
```

【反例】

```rust
fn main() {
    if lorem_ipsum
       && dolor_sit // 注意：这里缩进只是三个空格，仅仅是和前一行 `lorem_ipsum`对齐
       && amet_consectetur
       && lorem_sit
       && dolor_consectetur
       && amet_ipsum
       && lorem_consectetur
    {
        // ...
    }
}
```

#### 函数参数

【正例】

```rust
fn lorem() {}

fn lorem(ipsum: usize) {}

fn lorem(
    ipsum: usize,
    dolor: usize,
    sit: usize,
    amet: usize,
    consectetur: usize,
    adipiscing: usize,
    elit: usize,
) {
    // body
}

```

【反例】

```rust
fn lorem() {}

fn lorem(ipsum: usize) {}

fn lorem(ipsum: usize,
         dolor: usize,
         sit: usize,
         amet: usize,
         consectetur: usize,
         adipiscing: usize,
         elit: usize) {
    // body
}
```

#### 函数调用

【正例】

```rust
fn main() {
    lorem(
        "lorem",
        "ipsum",
        "dolor",
        "sit",
        "amet",
        "consectetur",
        "adipiscing",
        "elit",
    );
}
```

【反例】

```rust
fn main() {
    lorem("lorem",
          "ipsum",
          "dolor",
          "sit",
          "amet",
          "consectetur",
          "adipiscing",
          "elit");
}
```

#### 泛型

【正例】

```rust
fn lorem<
    Ipsum: Eq = usize,
    Dolor: Eq = usize,
    Sit: Eq = usize,
    Amet: Eq = usize,
    Adipiscing: Eq = usize,
    Consectetur: Eq = usize,
    Elit: Eq = usize,
>(
    ipsum: Ipsum,
    dolor: Dolor,
    sit: Sit,
    amet: Amet,
    adipiscing: Adipiscing,
    consectetur: Consectetur,
    elit: Elit,
) -> T {
    // body
}
```

【反例】

```rust
fn lorem<Ipsum: Eq = usize,
         Dolor: Eq = usize,
         Sit: Eq = usize,
         Amet: Eq = usize,
         Adipiscing: Eq = usize,
         Consectetur: Eq = usize,
         Elit: Eq = usize>(
    ipsum: Ipsum,
    dolor: Dolor,
    sit: Sit,
    amet: Amet,
    adipiscing: Adipiscing,
    consectetur: Consectetur,
    elit: Elit)
    -> T {
    // body
}
```

#### 结构体

【正例】

```rust
fn main() {
    let lorem = Lorem {
        ipsum: dolor,
        sit: amet,
    };
}
```

【反例】

```rust
fn main() {
    let lorem = Lorem { ipsum: dolor,
                        sit: amet };
}
```

## G.FMT.08 换行样式以文件自动检测为主

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`newline_style`](https://rust-lang.github.io/rustfmt/?#newline_style) | Auto（默认） | Yes| 换行样式以文件自动检测为主 |

### 【描述】

换行样式是基于每个文件自动检测的。 具有混合行尾的文件将转换为第一个检测到的行尾样式。

不同平台换行符不同：

- `Windows` 以 `\r\n`结尾。
- `Unix` 以 `\n` 结尾。


## G.FMT.09 当有多行表达式操作时，操作符应该置于行首

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`binop_separator`](https://rust-lang.github.io/rustfmt/?#binop_separator) | Front（默认） | No| 换行后，操作符置于行首 |

### 【描述】

当有多行表达式操作时，操作符应该置于行首。

### 【示例】

【正例】

操作符置于行首

```rust
fn main() {
    let or = foofoofoofoofoofoofoofoofoofoofoofoofoofoofoofoo
        || barbarbarbarbarbarbarbarbarbarbarbarbarbarbarbar;

    let sum = 123456789012345678901234567890
        + 123456789012345678901234567890
        + 123456789012345678901234567890;

    let range = aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
        ..bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb;
}
```

【反例】

操作符置于行尾

```rust
fn main() {
    let or = foofoofoofoofoofoofoofoofoofoofoofoofoofoofoofoo ||
        barbarbarbarbarbarbarbarbarbarbarbarbarbarbarbar;

    let sum = 123456789012345678901234567890 +
        123456789012345678901234567890 +
        123456789012345678901234567890;

    let range = aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa..
        bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb;
}
```

## G.FMT.10 枚举变体和结构体字段相互之间默认左对齐

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`enum_discrim_align_threshold`](https://rust-lang.github.io/rustfmt/?#enum_discrim_align_threshold) | 0（默认） | No|  具有判别式的枚举变体与其他变体进行垂直对齐的最大长度。没有判别符的变体在对齐时将被忽略。|
| [`struct_field_align_threshold`](https://rust-lang.github.io/rustfmt/?#struct_field_align_threshold) | 0（默认） | No|  结构体字段垂直对齐的最大长度|

### 【描述】

对于自定义了判别式的枚举体，和有字段的结构体而言，默认只需要左对齐就可以。这个宽度可以设置为任意值，但默认是0。此宽度并不是指插入多少空格，而是指需要对齐的字符长度。

### 【示例】

【正例】

```rust

enum Bar {
    A = 0,
    Bb = 1,
    RandomLongVariantGoesHere = 10,
    Ccc = 71,
}

enum Bar {
    VeryLongVariantNameHereA = 0,
    VeryLongVariantNameHereBb = 1,
    VeryLongVariantNameHereCcc = 2,
}
```


【反例】

当 `enum_discrim_align_threshold = 20` 时。

```rust
enum Foo {
    A   = 0,
    Bb  = 1,
    RandomLongVariantGoesHere = 10, // 注意，该变体长度已经超过了20，所以它不会被对齐
    Ccc = 2,
}

enum Bar {
    VeryLongVariantNameHereA = 0, // 注意，该变体长度已经超过了20，所以它不会被对齐
    VeryLongVariantNameHereBb = 1, // 注意，该变体长度已经超过了20，所以它不会被对齐
    VeryLongVariantNameHereCcc = 2,// 注意，该变体长度已经超过了20，所以它不会被对齐
}
```

当 `enum_discrim_align_threshold = 50` 时。

```rust

enum Foo {
    A                         = 0,
    Bb                        = 1,
    RandomLongVariantGoesHere = 10, // 注意，该变体长度未超过50，所以它会被对齐
    Ccc                       = 2,
}

enum Bar {
    VeryLongVariantNameHereA   = 0, // 注意，该变体长度未超过50，所以它会被对齐
    VeryLongVariantNameHereBb  = 1, // 注意，该变体长度未超过50，所以它会被对齐
    VeryLongVariantNameHereCcc = 2, // 注意，该变体长度未超过50，所以它会被对齐
}
```


## G.FMT.11 多个函数参数和导入模块的布局

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`fn_args_layout`](https://rust-lang.github.io/rustfmt/?#fn_args_layout) | Tall（默认） | Yes|  函数参数五个或以内可以一行，超过五个则使用块状缩进|
| [`imports_layout`](https://rust-lang.github.io/rustfmt/?#imports_layout) | Mixed（默认） | No| 导入模块每行超过四个则换行 |

### 【描述】

1. 五个以内函数参数可以置于一行，超过五个则使用「块」状缩进。
2. 导入模块每行超过四个，则换行。

### 【示例】

【正例】


```rust

trait Lorem {
    fn lorem(ipsum: Ipsum, dolor: Dolor, sit: Sit, amet: Amet, consectetur: Consectetur);

    fn lorem(ipsum: Ipsum, dolor: Dolor, sit: Sit, amet: Amet) {
        // body
    }

    fn lorem(
        ipsum: Ipsum,
        dolor: Dolor,
        sit: Sit,
        amet: Amet,
        consectetur: Consectetur,
        adipiscing: Adipiscing,
        elit: Elit,
    );

    fn lorem(
        ipsum: Ipsum,
        dolor: Dolor,
        sit: Sit,
        amet: Amet,
        consectetur: Consectetur,
        adipiscing: Adipiscing,
        elit: Elit,
    ) {
        // body
    }
}

use foo::{xxxxxxxxxxxxxxxxxx, yyyyyyyyyyyyyyyyyy, zzzzzzzzzzzzzzzzzz};

use foo::{
    aaaaaaaaaaaaaaaaaa, bbbbbbbbbbbbbbbbbb, cccccccccccccccccc, dddddddddddddddddd,
    eeeeeeeeeeeeeeeeee,
};
```

【反例】

当 `fn_args_layout` 和 `imports_layout` 被设置为其他值时：

```rust
trait Lorem {
    fn lorem(ipsum: Ipsum, dolor: Dolor, sit: Sit, amet: Amet);

    fn lorem(ipsum: Ipsum, dolor: Dolor, sit: Sit, amet: Amet) {
        // body
    }

    fn lorem(
        ipsum: Ipsum, dolor: Dolor, sit: Sit, amet: Amet, consectetur: Consectetur,
        adipiscing: Adipiscing, elit: Elit,
    );

    fn lorem(
        ipsum: Ipsum, dolor: Dolor, sit: Sit, amet: Amet, consectetur: Consectetur,
        adipiscing: Adipiscing, elit: Elit,
    ) {
        // body
    }
}

use foo::{xxxxxxxxxxxxxxxxxx, yyyyyyyyyyyyyyyyyy, zzzzzzzzzzzzzzzzzz};

use foo::{
    aaaaaaaaaaaaaaaaaa,
    bbbbbbbbbbbbbbbbbb,
    cccccccccccccccccc,
    dddddddddddddddddd,
    eeeeeeeeeeeeeeeeee,
    ffffffffffffffffff,
};

```

## G.FMT.12  空格使用规则

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`space_after_colon`](https://rust-lang.github.io/rustfmt/?#space_after_colon) | true（默认） | No |  在冒号后面要加空格|
| [`space_before_colon`](https://rust-lang.github.io/rustfmt/?#space_before_colon) | false（默认） | No| 在冒号前面不要加空格 |
| [`spaces_around_ranges`](https://rust-lang.github.io/rustfmt/?#spaces_around_ranges) | false（默认） | No| 在`..`和`..=`范围操作符前后不要加空格 |
| [`type_punctuation_density`](https://rust-lang.github.io/rustfmt/?#type_punctuation_density) | "Wide"（默认） | No| 在 `+`或`=`操作符前后要加空格（此处特指类型签名） |


### 【描述】

总结：

1. 在冒号之后添加空格，在冒号之前不要加空格。
2. 在范围（range）操作符（`..`和`..=`）前后不要使用空格。
3. 在`+`或`=`操作符前后要加空格。

### 【示例】

【正例】

```rust
// 当 `space_after_colon=true`
fn lorem<T: Eq>(t: T) {
    let lorem: Dolor = Lorem {
        ipsum: dolor,
        sit: amet,
    };
}

// 当 `space_before_colon=false`
fn lorem<T: Eq>(t: T) {
    let lorem: Dolor = Lorem {
        ipsum: dolor,
        sit: amet,
    };
}

// 当 `spaces_around_ranges=false`
let lorem = 0..10;
let ipsum = 0..=10;

// 当 `type_punctuation_density="Wide"`
fn lorem<Ipsum: Dolor + Sit = Amet>() {
    // body
    let answer = 1 + 2;
}
```


【反例】

```rust
// 当 `space_after_colon=false`
fn lorem<T:Eq>(t:T) {
    let lorem:Dolor = Lorem {
        ipsum:dolor,
        sit:amet,
    };
}

// 当 `space_before_colon=true`
fn lorem<T : Eq>(t : T) {
    let lorem : Dolor = Lorem {
        ipsum : dolor,
        sit : amet,
    };
}

// 当 `spaces_around_ranges=true`
let lorem = 0 .. 10;
let ipsum = 0 ..= 10;

// 当 `type_punctuation_density="Compressed"`
fn lorem<Ipsum: Dolor+Sit=Amet>() {
    // body
    let answer = 1 + 2;
}
```



## G.FMT.13  结尾逗号规则

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`trailing_comma`](https://rust-lang.github.io/rustfmt/?#trailing_comma) | "Vertical"（默认） | No |  当多个字段在不同行时，在最后一个字段结尾添加逗号，如果在同一行，则不加逗号|
| [`match_block_trailing_comma`](https://rust-lang.github.io/rustfmt/?#match_block_trailing_comma) | false（默认） | No| 在match分支中，如果包含了块，则不需要加逗号，否则需要加 |


### 【描述】

1. 当多个字段在不同行时，在最后一个字段结尾添加逗号，如果在同一行，则不加逗号。
2. 在match分支中，如果包含了块，则不需要加逗号，否则需要加。


### 【示例】

【正例】

```rust
// 当 `trailing_comma="Vertical"`
fn main() {
    let Lorem { ipsum, dolor, sit } = amet;
    let Lorem {
        ipsum,
        dolor,
        sit,
        amet,
        consectetur,
        adipiscing,
    } = elit;
}

// 当 `match_block_trailing_comma=false`
fn main() {
    match lorem {
        Lorem::Ipsum => {
            println!("ipsum");
        }
        Lorem::Dolor => println!("dolor"),
    }
}

```

【反例】

```rust
// 当 `trailing_comma="Always"`
fn main() {
    let Lorem { ipsum, dolor, sit, } = amet;
    let Lorem {
        ipsum,
        dolor,
        sit,
        amet,
        consectetur,
        adipiscing,
    } = elit;
}

// 当 `trailing_comma="Never"`
fn main() {
    let Lorem { ipsum, dolor, sit } = amet;
    let Lorem {
        ipsum,
        dolor,
        sit,
        amet,
        consectetur,
        adipiscing
    } = elit;
}

// 当 `match_block_trailing_comma=true`
fn main() {
    match lorem {
        Lorem::Ipsum => {
            println!("ipsum");
        },
        Lorem::Dolor => println!("dolor"),
    }
}

```



## G.FMT.14  `match` 分支格式


### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`match_arm_blocks`](https://rust-lang.github.io/rustfmt/?#match_arm_blocks) | true（默认） | No | 当match分支右侧代码体太长无法和`=>`置于同一行需要使用块(block)来包裹|
| [`match_arm_leading_pipes`](https://rust-lang.github.io/rustfmt/?#match_arm_leading_pipes) | Never（默认） | No| 在match分支左侧匹配表达式前不要增加管道符(`|`) |

### 【描述】

1. 当match分支右侧代码体太长无法和`=>`置于同一行需要使用块(block)来包裹。
2. 在match分支左侧匹配表达式前不要增加管道符(`|`)

### 【示例】

【正例】

```rust
// 当 `match_arm_blocks=true`
fn main() {
    match lorem {
        ipsum => { 
            foooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo(x)
        }
        dolor => println!("{}", sit),
        sit => foo(
            "foooooooooooooooooooooooo",
            "baaaaaaaaaaaaaaaaaaaaaaaarr",
            "baaaaaaaaaaaaaaaaaaaazzzzzzzzzzzzz",
            "qqqqqqqqquuuuuuuuuuuuuuuuuuuuuuuuuuxxx",
        ),
    }
}

// 当 `match_arm_leading_pipes="Never"`
fn foo() {
    match foo {
        "foo" | "bar" => {}
        "baz"
        | "something relatively long"
        | "something really really really realllllllllllllly long" => println!("x"),
        "qux" => println!("y"),
        _ => {}
    }
}



```

【反例】

```rust
// 当 `match_arm_blocks=false`
fn main() {
    match lorem {
        ipsum => 
            foooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo(x),
        dolor => println!("{}", sit),
        sit => foo(
            "foooooooooooooooooooooooo",
            "baaaaaaaaaaaaaaaaaaaaaaaarr",
            "baaaaaaaaaaaaaaaaaaaazzzzzzzzzzzzz",
            "qqqqqqqqquuuuuuuuuuuuuuuuuuuuuuuuuuxxx",
        ),
    }
}

// 当 `match_arm_leading_pipes="Alaways"`
fn foo() {
    match foo {
        | "foo" | "bar" => {}
        | "baz"
        | "something relatively long"
        | "something really really really realllllllllllllly long" => println!("x"),
        | "qux" => println!("y"),
        | _ => {}
    }
}

```

## G.FMT.15  导入模块分组规则

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`imports_granularity`](https://rust-lang.github.io/rustfmt/?#imports_granularity) | （Preserve（默认），Crate(推荐)）| No | 默认保留开发者的模块导入顺序|
| [`reorder_imports`](https://rust-lang.github.io/rustfmt/?#reorder_imports) | true（默认） | No| 模块分组内根据模块首字母按字典序进行排序 |
| [`group_imports`](https://rust-lang.github.io/rustfmt/?#group_imports) | （Preserve（默认）， StdExternalCrate（建议）） | No| 默认保留开发者的模块导入分组 |

### 【描述】

1. 导入同一模块的类型，应该置于同一个块内（`imports_granularity="Crate"`）。
2. 模块导入应该按以下规则进行分组（`group_imports="StdExternalCrate"`）：
    - 导入来自 `std`、`core` 和 `alloc`的模块需要置于前面一组。
    - 导入来自 第三方库的模块 应该置于中间一组。
    - 导入来自本地 `self`、`super`和`crate`前缀的模块，置于后面一组。
3. 分组内使用字典序进行排序（`reorder_imports=true`）。

说明： 默认 rustfmt 不会对导入的模块自动分组，而是保留开发者的导入顺序。所以，这里需要修改rustfmt 默认配置，但因为这几个配置项暂时未稳定，所以需要在 Nightly 下使用。

### 【示例】

【正例】

```rust
// 当 `imports_granularity="Crate"`
use foo::{
    a, b,
    b::{f, g},
    c,
    d::e,
};
use qux::{h, i};


// 当 `group_imports="StdExternalCrate` 且 `reorder_imports=true`
use alloc::alloc::Layout;
use core::f32;
use std::sync::Arc;

use broker::database::PooledConnection;
use chrono::Utc;
use juniper::{FieldError, FieldResult};
use uuid::Uuid;

use super::schema::{Context, Payload};
use super::update::convert_publish_payload;
use crate::models::Event;
```

【反例】

```rust

// 当 `imports_granularity="Preserve"`
use foo::b;
use foo::b::{f, g};
use foo::{a, c, d::e};
use qux::{h, i};


// 当按默认值设置时，模块导入比较乱，影响可读性
use super::update::convert_publish_payload;
use chrono::Utc;

use alloc::alloc::Layout;
use juniper::{FieldError, FieldResult};
use uuid::Uuid;

use std::sync::Arc;

use broker::database::PooledConnection;

use super::schema::{Context, Payload};
use crate::models::Event;
use core::f32;
```


## G.FMT.16  声明宏分支格式

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`format_macro_matchers`](https://rust-lang.github.io/rustfmt/?#format_macro_matchers) | （false（默认），true(建议)） | No |声明宏 模式匹配分支（`=>` 左侧）中要使用紧凑格式|
| [`format_macro_bodies`](https://rust-lang.github.io/rustfmt/?#format_macro_bodies) | true（默认） | No| 声明宏分支代码体（`=>` 右侧） 使用宽松格式|

### 【描述】

1. 在声明宏中，模式匹配分支（`=>` 左侧）应该使用紧凑格式（`format_macro_matchers=true`）。
2. 而分支代码体（`=>` 右侧） 使用宽松格式。详细请看示例。

一切都是为了提升可读性。

说明：因为这里需要修改`format_macro_matchers`的默认值，且该配置项并未 Stable ，所以需要 Nightly 下格式化。

### 【示例】

【正例】

```rust
// 当 `format_macro_matchers=true` 且 `format_macro_bodies=true`
macro_rules! foo {
    // 匹配分支紧凑格式， `$a:ident` 和 `$b:ty` 各自配对
    ($a:ident : $b:ty) => {
        $a(42): $b; // 在代码体内，则宽松一点
    };
    ($a:ident $b:ident $c:ident) => {
        $a = $b + $c;
    };
}
```

【反例】

```rust
// 当 `format_macro_matchers=false`且 `format_macro_bodies=true`
macro_rules! foo {
    ($a: ident : $b: ty) => {
        $a(42): $b;
    };
    ($a: ident $b: ident $c: ident) => {
        $a = $b + $c;
    };
}

// 当 `format_macro_matchers=false`且 `format_macro_bodies=false`
macro_rules! foo {
    ($a: ident : $b: ty) => {
        $a(42):$b;
    };
    ($a: ident $b: ident $c: ident) => {
        $a=$b+$c;
    };
}

```



## G.FMT.17  具名结构体字段初始化不要省略字段名

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`use_field_init_shorthand`](https://rust-lang.github.io/rustfmt/?#use_field_init_shorthand) | false（默认） | Yes |具名结构体字段初始化不能省略字段名|


### 【描述】

具名结构体字段初始化不能省略字段名。

### 【示例】

【正例】

```rust

struct Foo {
    x: u32,
    y: u32,
    z: u32,
}

fn main() {
    let x = 1;
    let y = 2;
    let z = 3;
    let a = Foo { x: x, y: y, z: z };
}
```

【反例】

```rust
struct Foo {
    x: u32,
    y: u32,
    z: u32,
}

fn main() {
    let x = 1;
    let y = 2;
    let z = 3;
    let a = Foo { x, y, z };
}
```


## G.FMT.18  extern 外部函数需要指定 ABI

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`force_explicit_abi`](https://rust-lang.github.io/rustfmt/?#force_explicit_abi) | true（默认） | Yes|  extern 外部函数总是要指定 ABI |


### 【描述】

当使用 `extern` 指定外部函数时，建议显式指定 `C-ABI`。`extern` 不指定的话默认就是 `C-ABI`，但是 Rust 语言显式指定是一种约定俗成。如果是 `Rust-ABI`则不会省略。


### 【示例】

【正例】

```rust
extern "C" {
    pub static lorem: c_int;
}

extern "Rust" {
    type MyType;
    fn f(&self) -> usize;
}
```

【反例】

```rust
// 省略 ABI 指定，则默认是 C-ABI
extern {
    pub static lorem: c_int;
}

// 非 C-ABI 是无法省略的
extern "Rust" {
    type MyType;
    fn f(&self) -> usize;
}
```



## G.FMT.19   解构元组的时候允许使用`..`来指代剩余元素

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项                                                     | 可选值                      | 是否 stable | 说明                                         |
| ------------------------------------------------------------ | --------------------------- | ----------- | -------------------------------------------- |
| [`condense_wildcard_suffixes`](https://rust-lang.github.io/rustfmt/?#condense_wildcard_suffixes) | false（默认） true （推荐） | No          | 解构元组的时候是否允许使用`..`来指代剩余元素 |


### 【描述】

默认选项是 false，表示不允许 解构元组的时候使用`..`来指代剩余元素

### 【示例】

【正例】

设置 `condense_wildcard_suffixes = true` :

```rust
fn main() {
    let (lorem, ipsum, ..) = (1, 2, 3, 4);
}
```

【反例】

```rust
fn main() {
    let (lorem, ipsum, _, _) = (1, 2, 3, 4);
    let (lorem, ipsum, ..) = (1, 2, 3, 4);
}
```

## G.FMT.20    不要将多个 Derive 宏合并为同一行

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项                                                     | 可选值                     | 是否 stable | 说明                             |
| ------------------------------------------------------------ | -------------------------- | ----------- | -------------------------------- |
| [`merge_derives`](https://rust-lang.github.io/rustfmt/?#merge_derives) | true（默认） false（推荐） | Yes         | 是否将多个 Derive 宏合并为同一行 |


### 【描述】

不要将多个 Derive 宏合并为同一行，可以增加代码可读性，明确语义。

### 【示例】

【正例】

修改默认设置 `merge_derives = false`

```rust
#[derive(Eq, PartialEq)]
#[derive(Debug)]
#[derive(Copy, Clone)]
pub enum Foo {}
```

【反例】

用默认设置  `merge_derives = true`

```rust
#[derive(Eq, PartialEq, Debug, Copy, Clone)]
pub enum Foo {}
```

