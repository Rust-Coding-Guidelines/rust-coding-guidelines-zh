## G.FMT.07 枚举变体和结构体字段相互之间默认左对齐

**【级别】** 建议

**【描述】**

对于自定义了判别式的枚举体，和有字段的结构体而言，默认只需要左对齐就可以。这个宽度可以设置为任意值，但默认是0。此宽度并不是指插入多少空格，而是指需要对齐的字符长度。

**【反例】**

当 `enum_discrim_align_threshold = 20` 时。

```rust
enum Foo {
    A   = 0,
    Bb  = 1,
    RandomLongVariantGoesHere = 10, // 注意，该变体长度已经超过了20，所以它不会被对齐
    Ccc = 2,
}

enum Bar {
    VeryLongVariantNameHereA = 0, // 注意，该变体长度已经超过了20，所以它不会被对齐
    VeryLongVariantNameHereBb = 1, // 注意，该变体长度已经超过了20，所以它不会被对齐
    VeryLongVariantNameHereCcc = 2,// 注意，该变体长度已经超过了20，所以它不会被对齐
}
```

当 `enum_discrim_align_threshold = 50` 时。

```rust

enum Foo {
    A                         = 0,
    Bb                        = 1,
    RandomLongVariantGoesHere = 10, // 注意，该变体长度未超过50，所以它会被对齐
    Ccc                       = 2,
}

enum Bar {
    VeryLongVariantNameHereA   = 0, // 注意，该变体长度未超过50，所以它会被对齐
    VeryLongVariantNameHereBb  = 1, // 注意，该变体长度未超过50，所以它会被对齐
    VeryLongVariantNameHereCcc = 2, // 注意，该变体长度未超过50，所以它会被对齐
}
```

**【正例】**

```rust

enum Bar {
    A = 0,
    Bb = 1,
    RandomLongVariantGoesHere = 10,
    Ccc = 71,
}

enum Bar {
    VeryLongVariantNameHereA = 0,
    VeryLongVariantNameHereBb = 1,
    VeryLongVariantNameHereCcc = 2,
}
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`enum_discrim_align_threshold`](https://rust-lang.github.io/rustfmt/?#enum_discrim_align_threshold) | 0（默认） | No|  具有判别式的枚举变体与其他变体进行垂直对齐的最大长度。没有判别符的变体在对齐时将被忽略。|
| [`struct_field_align_threshold`](https://rust-lang.github.io/rustfmt/?#struct_field_align_threshold) | 0（默认） | No|  结构体字段垂直对齐的最大长度|
