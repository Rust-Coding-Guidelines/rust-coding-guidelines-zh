# Rustfmt 配置相关说明

## 在 Stable Rust 下使用未稳定配置项的方法

1. CI Job 可以分为 `Stable` 和 `Nightly`。在 `Stable CI` 下进行编译，在`Nightly CI`下执行`cargo fmt` 和 `cargo clippy`。
2. 在项目本地可以使用 `cargo +nightly fmt` 代替 `cargo fmt`。

注意： 一定要在文件保存之后再运行 rustfmt`，否则容易出错。

## 真实项目中的配置案例

1. 来自 [Rust 语言自身项目](https://github.com/rust-lang/rust/blob/master/rustfmt.toml)。

```toml
# Run rustfmt with this config (it should be picked up automatically).
version = "Two"
use_small_heuristics = "Max"
merge_derives = false

# by default we ignore everything in the repository
# tidy only checks files which are not ignored, each entry follows gitignore style
ignore = [
    "/build/",
    "/*-build/",
    "/build-*/",
    "/vendor/",

    # tests for now are not formatted, as they are sometimes pretty-printing constrained
    # (and generally rustfmt can move around comments in UI-testing incompatible ways)
    "src/test",

    # do not format submodules
    "library/backtrace",
    "library/stdarch",
    "compiler/rustc_codegen_cranelift",
    "src/doc/book",
    "src/doc/edition-guide",
    "src/doc/embedded-book",
    "src/doc/nomicon",
    "src/doc/reference",
    "src/doc/rust-by-example",
    "src/doc/rustc-dev-guide",
    "src/llvm-project",
    "src/tools/cargo",
    "src/tools/clippy",
    "src/tools/miri",
    "src/tools/rls",
    "src/tools/rust-analyzer",
    "src/tools/rustfmt",
    "src/tools/rust-installer",
]

```

1. 来自 [Google Fuchsia 操作系统](https://cs.opensource.google/fuchsia/fuchsia/+/main:rustfmt.toml)。

```toml
# Fuchsia Format Style
# last reviewed: Jan 29, 2019

# Fuchsia uses 2018 edition only
edition = "2018"

# The "Default" setting has a heuristic which splits lines too aggresively.
# We are willing to revisit this setting in future versions of rustfmt.
# Bugs:
#   * https://github.com/rust-lang/rustfmt/issues/3119
#   * https://github.com/rust-lang/rustfmt/issues/3120
use_small_heuristics = "Max"

# Prevent carriage returns
newline_style = "Unix"
```

2. 来自 [Tikv](https://github.com/tikv/tikv/blob/master/rustfmt.toml) 。

```toml
version = "Two"
unstable_features = true

condense_wildcard_suffixes = true
license_template_path = "etc/license.template"
newline_style = "Unix"
use_field_init_shorthand = true
use_try_shorthand = true
```


```toml

edition = "2018"
newline_style = "unix"
# comments
normalize_comments=true
wrap_comments=true
# imports 
imports_granularity="Crate"
group_imports="StdExternalCrate"

```


## 一些全局配置项

### rustfml 格式化版本

【描述】

 `Version::One` 向后兼容 Rustfmt 1.0。 其他版本仅在主要版本号内向后兼容。目前 `version` 可选值只有 `One` 和 `Two`。

【对应配置项】

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`version`](https://rust-lang.github.io/rustfmt/?#version) | One（默认） | No|  指定 rustfmlt 格式化版本 |

【示例】

```toml
# Run rustfmt with this config (it should be picked up automatically).
version = "Two"
```


### 指定文件或目录跳过格式化

【描述】

跳过与指定模式匹配的格式化文件和目录。 模式格式与 `.gitignore` 相同。 一定要使用 `Unix/forwardslash/style` 路径，此路径样式适用于所有平台。 不支持带有反斜杠 `\` 的 Windows 样式路径。

【对应配置项】

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`ignore`](https://rust-lang.github.io/rustfmt/?#ignore) | 格式化每一个Rust文件（默认） | No|  指定文件或目录跳过格式化 |

【示例】

```rust
// 跳过指定文件
ignore = [
    "src/types.rs",
    "src/foo/bar.rs",
]
// 跳过指定目录
ignore = [
    "examples",
]
// 跳过项目内所有文件
ignore = ["/"]
```


### 禁用格式化

【描述】

可以通过 `disable_all_formatting=true` 配置来禁用格式化。默认是开启的。

【对应配置项】

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`disable_all_formatting`](https://rust-lang.github.io/rustfmt/?#disable_all_formatting) | false（默认） | No|  禁止格式化 |


### 配置 edition 版次

【描述】

如果通过 Cargo 的格式化工具 cargo fmt 执行，Rustfmt 能够通过读取 Cargo.toml 文件来获取使用的版本。 否则，需要在配置文件中指定版本。

【对应配置项】

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`edition`](https://rust-lang.github.io/rustfmt/?#edition) | 2015（默认） | No| 配置 edition 版次 |

【示例】

```toml
edition = "2018"
```

### 开启未稳定特性

【描述】

默认未启用，但是可以通过配置此功能在 Nightly 上启用此功能。

【对应配置项】

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`unstable_features`](https://rust-lang.github.io/rustfmt/?#unstable_features) | false（默认） | No| 开启未稳定特性 |




## 每行最大宽度为 100 个字符

**【级别】** 建议

**【描述】**

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

**【反例】**

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

**【正例】**

`use_small_heuristics` 默认配置示例。


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

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- |
| [`max_width`](https://rust-lang.github.io/rustfmt/?#max_width) | 100 | yes（默认）| 行最大宽度默认值|
|[`error_on_line_overflow`](https://rust-lang.github.io/rustfmt/?#error_on_line_overflow)| false（默认）| No (tracking issue: #3391)| 如果超过最大行宽设置则报错|
|[`use_small_heuristics`](https://rust-lang.github.io/rustfmt/?#use_small_heuristics)| Default（默认）Max（推荐） | Yes| 统一管理宽度设置|


## 单行规则

**【级别】** 建议

**【描述】**

当语言项内容为空时，即空函数，空结构体，空实现等，要保持单独一行。但是，当函数中只有一个表达式时，请不要保持单行。

**【反例】**

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

**【正例】**

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

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 默认值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`empty_item_single_line`](https://rust-lang.github.io/rustfmt/?#empty_item_single_line) | true（默认） | No| 当语言项内容为空时，要保持单行 |
| [`fn_single_line`](https://rust-lang.github.io/rustfmt/?#fn_single_line) | false（默认） | No| 当函数中只有一个表达式时，不要保持单行 |
| [`struct_lit_single_line`](https://rust-lang.github.io/rustfmt/?#struct_lit_single_line) | true（默认） | No| 当结构体字面量中只有少量表达式时，要保持单行 |

## 换行样式以文件自动检测为主

**【级别】** 建议

**【描述】**

换行样式是基于每个文件自动检测的。 具有混合行尾的文件将转换为第一个检测到的行尾样式。

不同平台换行符不同：

- `Windows` 以 `\r\n`结尾。
- `Unix` 以 `\n` 结尾。

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`newline_style`](https://rust-lang.github.io/rustfmt/?#newline_style) | Auto（默认） | Yes| 换行样式以文件自动检测为主 |


## 结尾逗号规则

**【级别】** 建议

**【描述】**

1. 当多个字段在不同行时，在最后一个字段结尾添加逗号，如果在同一行，则不加逗号。
2. 在match分支中，如果包含了块，则不需要加逗号，否则需要加。

**【反例】**

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

**【正例】**

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

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`trailing_comma`](https://rust-lang.github.io/rustfmt/?#trailing_comma) | "Vertical"（默认） | No |  当多个字段在不同行时，在最后一个字段结尾添加逗号，如果在同一行，则不加逗号|
| [`match_block_trailing_comma`](https://rust-lang.github.io/rustfmt/?#match_block_trailing_comma) | false（默认） | No| 在match分支中，如果包含了块，则不需要加逗号，否则需要加 |
