## P.FMT.12 声明宏分支应该具有良好的可读性

**【描述】**

1. 在声明宏中，模式匹配分支（`=>` 左侧）应该使用紧凑格式（`format_macro_matchers=true`）。
2. 而分支代码体（`=>` 右侧） 使用宽松格式。

> 说明：因为这里需要修改`format_macro_matchers`的默认值，且该配置项并未 Stable ，所以需要在 Nightly 下修改配置项的值以便使用。

**【反例】**

```rust
// 不符合： 匹配分支使用了宽松格式
// 当 `format_macro_matchers=false`且 `format_macro_bodies=true`
macro_rules! foo {
    ($a: ident : $b: ty) => {
        $a(42): $b;
    };
    ($a: ident $b: ident $c: ident) => {
        $a = $b + $c;
    };
}

// 不符合： 分支代码体使用了紧凑格式
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

**【正例】**

```rust
// 当 `format_macro_matchers=true` 且 `format_macro_bodies=true`
macro_rules! foo {
    // 符合：匹配分支紧凑格式， `$a:ident` 和 `$b:ty` 各自配对
    ($a:ident : $b:ty) => {
        $a(42): $b; // 在代码体内，则宽松一点
    };
    // 符合
    ($a:ident $b:ident $c:ident) => {
        $a = $b + $c;
    };
}
```

**【rustfmt 配置】**

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`format_macro_matchers`](https://rust-lang.github.io/rustfmt/?#format_macro_matchers) | （false（默认），true(建议)） | No |声明宏 模式匹配分支（`=>` 左侧）中要使用紧凑格式|
| [`format_macro_bodies`](https://rust-lang.github.io/rustfmt/?#format_macro_bodies) | true（默认） | No| 声明宏分支代码体（`=>` 右侧） 使用宽松格式|
