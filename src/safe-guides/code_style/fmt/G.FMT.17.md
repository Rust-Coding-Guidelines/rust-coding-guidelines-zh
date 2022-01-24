## G.FMT.17  具名结构体字段初始化时字段名最好不要省略

**【级别】** 建议

**【描述】**

因为本规则严重依赖于rustfmt，而rustfmt会根据相应配置项对代码进行自动更改，为了确保不会因为rustfmt配置项的更改而导致代码错误，请在遵循rustfmt使用注意事项的基础上，遵循本规则：

1. 省略字段名的时候需要注意变量名和字段名保持一致。
2. 变量名和字段名不一致的情况下，最好不要省略字段名。

以此规则标题主要目的是来规范rustfmt的默认配置不会被人设置为`true`，而把代码修改错误。


**【反例】**

```rust
struct Foo {
    x: u32,
    y: u32,
    z: u32,
}

fn main() {
    let x = 1;
    let y = 2;
    let z = 3;
    let a = Foo { x, y, z };
}
```

**【正例】**

```rust

struct Foo {
    x: u32,
    y: u32,
    z: u32,
}

fn main() {
    let x = 1;
    let y = 2;
    let z = 3;
    let a = Foo { x: x, y: y, z: z };
}
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`use_field_init_shorthand`](https://rust-lang.github.io/rustfmt/?#use_field_init_shorthand) | false（默认） | Yes |具名结构体字段初始化不能省略字段名|
