## P.FMT.01  始终使用 rustfmt 进行自动格式化代码

**【描述】**

应该总是在项目中添加 `rustfmt.toml` 或 `.rustfmt.toml`文件，即使它是空文件。这是向潜在的合作者表明你希望代码是自动格式化的。

**【例外】**

在特殊的情况下，可以通过条件编译属性 `#[cfg_attr(rustfmt, rustfmt_skip)]` 或 `#[rustfmt::skip]` 来关闭自动格式化。

比如下面示例：

`vec!` 中的元素排布是固定格式，这样有助于开发的便利。

```rust
fn main() {
    let got = vec![
            0x00, 0x05, 0x01, 0x00,
            0xff,
            0x00,
            0x00,
    
            0x01, 0x0c, 0x02, 0x00,
            0xde, 0xad, 0xbe, 0xef, 0xde, 0xad, 0xbe, 0xef,
            b'd', b'e', b'a', b'd', b'b', b'e', b'e', b'f', 0x00,
            0x00,
    
            127, 0x06, 0x03, 0x00,
            0x01, 0x02,
            b'a', b'b', b'c', b'd', 0x00,
            b'1', b'2', b'3', b'4', 0x00,
            0x00,
    ];
}
```

如果使用 自动格式化，会变成：

```rust
fn main() {
    let got = vec![
        0x00, 0x05, 0x01, 0x00, 0xff, 0x00, 0x00, 0x01, 0x0c, 0x02, 0x00, 0xde, 0xad, 0xbe, 0xef,
        0xde, 0xad, 0xbe, 0xef, b'd', b'e', b'a', b'd', b'b', b'e', b'e', b'f', 0x00, 0x00, 127,
        0x06, 0x03, 0x00, 0x01, 0x02, b'a', b'b', b'c', b'd', 0x00, b'1', b'2', b'3', b'4', 0x00,
        0x00,
    ];
}
```

但是加上 `#[rustfmt::skip]` 就不会被自动格式化影响：

```rust
fn main() {
    #[rustfmt::skip] 
    let got = vec![
            0x00, 0x05, 0x01, 0x00,
            0xff,
            0x00,
            0x00,
    
            0x01, 0x0c, 0x02, 0x00,
            0xde, 0xad, 0xbe, 0xef, 0xde, 0xad, 0xbe, 0xef,
            b'd', b'e', b'a', b'd', b'b', b'e', b'e', b'f', 0x00,
            0x00,
    
            127, 0x06, 0x03, 0x00,
            0x01, 0x02,
            b'a', b'b', b'c', b'd', 0x00,
            b'1', b'2', b'3', b'4', 0x00,
            0x00,
    ];
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group |是否可定制|
| ------ | ---- | --------- | ------ | ------ |
|  _ | no | no | _ | yes |

【定制化建议】

通过检测 项目 根目录下是否存在 `rustfmt.toml` 或 `.rustfmt.toml` ，如果没有该文件，则发出警告，让开发者使用 rustfmt 来格式化代码。