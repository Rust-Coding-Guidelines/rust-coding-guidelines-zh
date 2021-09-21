# 泛型


## P.GEN.01 

【描述】


---


## G.GEN.01 不要在泛型位置上使用内建类型

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [builtin_type_shadow](https://rust-lang.github.io/rust-clippy/master/#builtin_type_shadow) | yes| no | style | warn |

### 【描述】


【正例】

```rust
impl<T> Foo<T> {
    fn impl_func(&self) -> T {
        42
    }
}
```

【反例】

```rust
impl<u32> Foo<u32> {
    fn impl_func(&self) -> u32 {
        42
    }
}
```