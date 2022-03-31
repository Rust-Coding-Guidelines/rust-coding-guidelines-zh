## P.FMT.13  具名结构体字段初始化时不要省略字段名

**【描述】**

因为本规则依赖于rustfmt，而rustfmt会根据相应配置项对代码进行自动更改，为了确保不会因为rustfmt配置项的更改而导致代码错误，请在遵循rustfmt使用注意事项的基础上遵循本规则：

1. 省略字段名的时候需要注意变量名和字段名保持一致。
2. 变量名和字段名不一致的情况下，不要省略字段名。

> 注意：如果将 rustfmt 默认配置 `use_field_init_shorthand`改为`true`时，有可能会发生代码被修改错误的情况。


**【反例】**

```rust
struct Foo {
    a: u32, // 注意这里是 a
    y: u32,
    z: u32,
}

fn main() {
    let x = 1;
    let y = 2;
    let z = 3;
    // 不符合： 如果允许省略字段名，并且rustfmt 配置 `use_field_init_shorthand`改为`true`时，
    //        下面代码中字段`a`就会被rustfmt删除，变为 `Foo{x, y, z}`，从而造成错误
    // rustfmt 无法检查这个错误，但是编译时能检查出来，所以要遵循rustfmt使用注意事项就不会出问题
    let a = Foo { a: x, y, z };
}
```

**【正例】**

```rust

struct Foo {
    a: u32,
    y: u32,
    z: u32,
}

fn main() {
    let x = 1;
    let y = 2;
    let z = 3;
    // 符合
    let a = Foo { a: x, y: y, z: z };
}
```

**【rustfmt 配置】**

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`use_field_init_shorthand`](https://rust-lang.github.io/rustfmt/?#use_field_init_shorthand) | false（默认） | Yes |具名结构体字段初始化不能省略字段名|
