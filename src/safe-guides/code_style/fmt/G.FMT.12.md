## G.FMT.12  声明宏分支格式

**【级别】** 建议

**【描述】**

1. 在声明宏中，模式匹配分支（`=>` 左侧）应该使用紧凑格式（`format_macro_matchers=true`）。
2. 而分支代码体（`=>` 右侧） 使用宽松格式。详细请看示例。

一切都是为了提升可读性。

说明：因为这里需要修改`format_macro_matchers`的默认值，且该配置项并未 Stable ，所以需要 Nightly 下格式化。

**【反例】**

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

**【正例】**

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

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`format_macro_matchers`](https://rust-lang.github.io/rustfmt/?#format_macro_matchers) | （false（默认），true(建议)） | No |声明宏 模式匹配分支（`=>` 左侧）中要使用紧凑格式|
| [`format_macro_bodies`](https://rust-lang.github.io/rustfmt/?#format_macro_bodies) | true（默认） | No| 声明宏分支代码体（`=>` 右侧） 使用宽松格式|
