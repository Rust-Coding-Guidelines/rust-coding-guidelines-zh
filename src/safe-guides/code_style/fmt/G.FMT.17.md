## G.FMT.17  具名结构体字段初始化不要省略字段名

**【级别】** 建议

**【描述】**

具名结构体字段初始化不能省略字段名。

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
