# P.GEN.01 代码生成要按情况选择使用过程宏还是 `build.rs`

**【描述】**

用过程宏生进行代码生成，比如生成新类型或函数，有一个缺点就是：IDE 无法识别它们，影响开发体验。

但是使用 `build.rs` 生成的代码，对 IDE 更友好。

不过随着 IDE 的增强，过程宏以后应该也能变得更加 IDE 友好。

建议按应用场景选择：

- `build.rs` 一般用于根据外部文件生成代码的场景。比如根据 `C` 头文件生成 Rust 绑定，或者根据 `proto` 文件生成相应的 Rust 类型等，供开发者直接使用。
- 过程宏一般用于消除样例式代码，提升库使用者的开发体验。

**【正例】**

`build.rs` 把 `tonic` 生成的代码直接放在 `src` 目录 (生成的代码文件应该在 .gitignore 中忽略版本管理)，这样 IDE 能够识别它们使自动完成能够工作，提高开发效率。

```rust
fn main() -> Result<(), Box<dyn std::error::Error>> {
    tonic_build::configure()
        .out_dir("src")
        .compile(
            &["proto/helloworld/helloworld.proto"],
            &["proto/helloworld"],
        )?;
    println!("cargo:rerun-if-changed=proto");
}
```

`tarpc`的`service`宏会生成一个新的`WorldClient`类型，IDE完全无法识别。

```rust
#[tarpc::service]
trait World {
    async fn hello(name: String) -> String;
}

let (client_transport, server_transport) = tarpc::transport::channel::unbounded();
let mut client = WorldClient::new(client::Config::default(), client_transport).spawn();
```
