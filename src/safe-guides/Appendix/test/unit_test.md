# 单元测试

Rust 支持单元测试。

## 测试代码组织

对于内部函数，单元测试代码最好放到业务代码的同一个模块下。

对于外部接口，单元测试最好放到独立的 `tests` 目录。

## 文档测试

对所有对外接口进行文档测试是一个不错的开始。

## 编译测试

通过 `compiletest` 来测试某些代码可能无法编译。 参考： [Rustc开发指南](https://rustc-dev-guide.rust-lang.org/tests/adding.html#ui)

## 随机测试

使用 第三方库`proptest` 来进行随机测试。

```rust
use proptest::prelude::*;

proptest! {
    #[test]
    fn check_count_correct(haystack: Vec<u8>, needle: u8) {
        prop_assert_eq!(count(&haystack, needle), naive_count(&haystack, needle));
    }
}
```

## 代码测试率覆盖检测工具

[tarpaulin](https://github.com/xd009642/tarpaulin) 是 Cargo 构建系统的代码覆盖率报告工具，目前 **仅支持运行 Linux 的 x86_64 处理器**。

