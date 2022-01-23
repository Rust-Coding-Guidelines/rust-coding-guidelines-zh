## G.CMT.03  在每一个文件开头加入版权公告、创建和修改年份和功能描述

**【级别】** 建议

**【描述】**

每个文件都应该包含许可证引用。为项目选择合适的许可证版本.(比如, Apache 2.0, BSD, LGPL, GPL)。

可以通过 `rustfmt` 的 `license_template_path` 配置项 和 `license.template`来自动化此操作。
另外还需要增加该文件的首次创建年份、最后修改年份，以及功能描述。但这两项无法通过rustfmt自动插入
对于文件修改时间，部分VSCode的一些插件也可以帮忙做到。

**【正例】**

可以命名为`.rustfmt.license-template`许可证模版。

```rust
// Copyright {\d+} YOURCOMPANY Project Authors. Licensed under Apache-2.0.
```

在 `rustfmt.toml` 中配置：

```toml
license_template_path = ".rustfmt.license-template"
```

在代码文件中手工添加对应的注释 （自动插入功能还未支持）：

```rust
// Copyright 2021 YOURCOMPANY Project Authors. Licensed under Apache-2.0.
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`license_template_path`](https://rust-lang.github.io/rustfmt/?#license_template_path) | 格式化每一个Rust文件（默认） | No|  指定许可证模版路径 |