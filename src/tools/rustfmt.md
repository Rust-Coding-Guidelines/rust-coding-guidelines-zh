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




## 其他