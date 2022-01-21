## G.CMT.03  在每一个文件开头加入版权公告

**【级别】** 建议

**【描述】**

每个文件都应该包含许可证引用。为项目选择合适的许可证版本.(比如, Apache 2.0, BSD, LGPL, GPL)。

可以通过 `rustfmt` 的 `license_template_path` 配置项 和 `license.template`来自动化此操作。

**【正例】**

来自 [TiKV](https://github.com/tikv/tikv/blob/master/etc/license.template) 项目的案例。可以命名为`.rustfmt.license-template`许可证模版。

```rust
// Copyright {\d+} TiKV Project Authors. Licensed under Apache-2.0.
```

在 `rustfmt.toml` 中配置：

```toml
license_template_path = ".rustfmt.license-template"
```

在代码文件中手工添加对应的注释 （自动插入功能还未支持）：

```rust
// Copyright 2021 TiKV Project Authors. Licensed under Apache-2.0.
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`license_template_path`](https://rust-lang.github.io/rustfmt/?#license_template_path) | 格式化每一个Rust文件（默认） | No|  指定许可证模版路径 |