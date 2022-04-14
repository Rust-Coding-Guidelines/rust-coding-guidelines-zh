# 1. 概述

## 状态

- 《Rust 编码规范》初稿发布 2021-10-31 (V 0.1)
- 《Rust 编码规范》经社区和公司内第一次评审版本发布 2022-02 (V 0.2) 
- 《Rust 编码规范》经社区和公司内第二次评审版本发布 2022-03 (V 0.3) 
- 《Rust 编码规范》经社区和公司内第三次评审版本发布 2022-04 (V 1.0 beta) 

## 详细

- [1.1 为什么需要 Rust 编码规范](./overview/why.md)
- [1.2 编码规范基本约定](./overview/convention.md)

## 介绍

Rust 语言社区内其实分散着很多编码规范，下面罗列一部分公开信息：

- [官方｜Rust API 编写指南](https://rust-lang.github.io/api-guidelines/about.html)
- [官方 | Rust Style Guide](https://github.com/rust-dev-tools/fmt-rfcs/blob/master/guide/guide.md)
- [Rust's Unsafe Code Guidelines Reference](https://rust-lang.github.io/unsafe-code-guidelines/)
- [法国国家信息安全局 | Rust 安全（Security）规范](https://anssi-fr.github.io/rust-guide)
- [Apache Teaclave 安全计算平台 | Rust 开发规范](https://teaclave.apache.org/docs/rust-guildeline/)
- [PingCAP | 编码风格指南（包括 Rust 和 Go 等）](https://github.com/pingcap/style-guide)
- [Google Fuchsia 操作系统 Rust 开发指南](https://fuchsia.dev/fuchsia-src/development/languages/rust)
- [RustAnalyzer 编码风格指南](https://github.com/rust-analyzer/rust-analyzer/blob/master/docs/dev/style.md)
- [使用 Rust 设计优雅的 API](https://deterministic.space/elegant-apis-in-rust.html)
- [Rust FFI 指南](https://michael-f-bryan.github.io/rust-ffi-guide/)

上面这些除了 Rust 官方和法国国家信息安全局的编码规范之外，其他开源项目的编码规范主要是为了规范贡献者们遵循一个统一的编码风格。

所以，一个通用的，覆盖编码风格和具体编码实践的全面的编码规范，更有助于社区各个开源项目和各大公司参考去制定自己的编码规范。

![org](./img/org.png)

本规范致力于成为统一的 Rust 编码规范，各大公司可以依赖本规范，结合自己的业务领域和团队习惯，形成自己的编码规范，并可以在日常实践中反哺本规范，让本规范更加完善。





