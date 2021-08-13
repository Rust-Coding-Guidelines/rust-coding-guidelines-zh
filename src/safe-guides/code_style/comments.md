# 注释与文档

在 Rust 中，注释分为两类：普通注释和文档注释，这里使用「注释」来代表普通注释，「文档」来代表「文档注释」。

---

## P.CMT.01 不到必要的时候不要添加注释

### 【描述】

注释固然很重要, 但最好的代码应当本身就是文档。有意义的类型名、函数名和变量名, 要远胜过要用注释解释的含糊不清的名字。当有意义的类型名、函数名和变量名还不能表达完整的语义时，再使用注释。

不要描述显而易见的现象, 永远不要用自然语言翻译代码作为注释。

## P.CMT.02 文档应该始终基于 `rustdoc` 工具来构建

### 【描述】

Rust 语言提供 `rustdoc` 工具来帮助构建文档，所以应该始终围绕`rustdoc`工具的特性来构建项目文档。

## P.CMT.03 文档应该围绕 What 和 How 为核心来构建

### 【描述】 

文档应该始终围绕两个方向来构建：

1. What : 用于阐述代码为什么而构建。
2. how : 用于阐述代码如何去使用。

## P.CMT.04 注释和文档应该保持简短精干

### 【描述】

1. 文档内容用语应该尽量简短精干，不宜篇幅过长。请确保你的代码注释良好并且易于他人理解。
2. 使用通俗易懂的描述而尽量避免使用专业技术术语。好的注释能够传达上下文关系和代码目的。

## P.CMT.05 注释和文档使用的自然语言要保持一致

### 【描述】 

注释和文档尽量使用英文来填写，如果要使用中文，整个项目必须都使用中文。请确保整个项目中文档和注释都使用同一种文本语言，保持一致性。


---

## G.CMT.01 在每一个文件开头加入版权公告

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`license_template_path`](https://rust-lang.github.io/rustfmt/?#license_template_path) | 格式化每一个Rust文件（默认） | No|  指定许可证模版路径 |

### 【描述】

每个文件都应该包含许可证引用。为项目选择合适的许可证版本.(比如, Apache 2.0, BSD, LGPL, GPL)。

可以通过 `rustfmt` 的 `license_template_path` 配置项 和 `license.template`来自动化此操作。

### 【示例】

来自 [TiKV](https://github.com/tikv/tikv/blob/master/etc/license.template) 项目的案例，`license.template`许可证模版。

```rust
// Copyright {\d+} TiKV Project Authors. Licensed under Apache-2.0.
```

在 `rustfmt.toml` 中配置：

```toml
license_template_path = "etc/license.template"
```

在代码文件中手工添加对应的注释：

```rust
// Copyright 2021 TiKV Project Authors. Licensed under Apache-2.0.
```










---

## 参考

1. [RFC 505: API 注释约定](https://github.com/rust-lang/rfcs/blob/master/text/0505-api-comment-conventions.md)
2. [RFC 1574: API 文档约定](https://github.com/rust-lang/rfcs/blob/master/text/1574-more-api-documentation-conventions.md)
3. [Making Great Docs with Rustdoc](https://www.tangramvision.com/blog/making-great-docs-with-rustdoc)
4. [Rust Doc book](https://doc.rust-lang.org/rustdoc/what-is-rustdoc.html)

