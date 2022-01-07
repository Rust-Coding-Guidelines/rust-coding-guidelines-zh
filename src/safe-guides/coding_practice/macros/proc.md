# 过程宏

[过程宏（*Procedural macros*）](https://doc.rust-lang.org/reference/procedural-macros.html) 允许开发者来创建语法扩展。你可以通过过程宏创建类似函数的宏、派生宏以及属性宏。

广义上的"过程宏"指的是通过 syn/quote(毕竟几乎全部过程宏库都用 syn) 及 syn 生态(例如 darling) 进行代码生成等元编程操作。

syn/quote 不仅能用于过程宏，还广泛用于代码生成(*codegen*)、静态分析等用途，例如 tonic-build/prost 源码中也用到了 syn/quote 。

因此本过程宏规范不仅适用于过程宏，部分规范(例如 [P.MAC.Proc.06](safe-guides/coding_pactice/macros/proc/P.MAC.Proc.06.md))还适用于 prost 这种代码生成库

过程宏必须被单独定义在一个类型为`proc-macro` 的 crate 中。

过程宏有两类报告错误的方式：`Panic` 或 通过 `compile_error`  宏调用发出错误。

过程宏不具有卫生性（hygiene），这意味着它会受到外部语法项（item）的影响，也会影响到外部导入。

过程宏可以在编译期执行任意代码。

---
<!-- toc -->
---

## P.MAC.Proc.01  不要使用过程宏来规避静态分析检查

【描述】

不要利用过程宏来定义能规避 Rust 静态分析检查的宏。

【正例】

对于不安全的函数，应该显式地使用 `unsafe` 。这样做的好处是利用 Rust 编译器静态检查传播 unsafe 调用链条，以达到可以全局查找 unsafe 使用来消除一些代码隐患，方便定位 问题。

```rust
unsafe fn super_safe(x: f32) -> i32 {
    unsafe { std::mem::transmute::<f32, i32>(x) }
}

unsafe fn deref_null() {
    unsafe { *std::ptr::null::<u8>(); }
}

fn main(){
    println!("{:?}", unsafe{super_safe(1.0f32)}); // 1065353216
    // error[E0133]: call to unsafe function is unsafe and requires unsafe function or block
    // deref_null();  // 如果调用 unsafe 函数不加 unsafe 块，编译器就会报错。
    unsafe{ deref_null(); }
}
```

【反例】

在 Rust 生态中有一个库 [`plutonium`](https://github.com/mxxo/plutonium) ，该库利用了过程宏来消除代码中直接的  `unsafe`  块的使用，从而规避了编译器对 Unsafe 关键字的静态检查。

该库会通过`#[safe]` 过程宏在自动生成代码的时候为函数体添加   `unsafe` 块，但这会影响到 `unsafe` 调用链依赖静态检查传播，从而进一步打断 unsafe 调用链路，影响后续通过 `unsafe` 关键字来定位问题。

   ```rust
   use plutonium::safe;
   
   #[safe]
   fn super_safe(x: f32) -> i32 {
       std::mem::transmute::<f32, i32>(x)
   }
   
   #[safe]
   unsafe fn deref_null() {
       *std::ptr::null::<u8>();
   }
   
   fn main(){
       println!("{:?}", super_safe(1.0));
       deref_null();
   }
   ```



【相关讨论】

- [RUSTSEC-2020-0011](https://rustsec.org/advisories/RUSTSEC-2020-0011.html)
- [https://github.com/RustSec/advisory-db/issues/275](https://github.com/RustSec/advisory-db/issues/275)
- [https://github.com/rust-lang/unsafe-code-guidelines/issues/278](https://github.com/rust-lang/unsafe-code-guidelines/issues/278)

## P.MAC.Proc.02  实现过程宏时要对关键特性增加测试

【描述】

实现过程宏的时候，要对关键特性增加测试，这是为了避免出现关键特性遗漏的情况。 

【正例】

在第三方库 [zeroize](https://github.com/iqlusioninc/crates/tree/main/zeroize) 中，曾经因为过程宏中对枚举类型没有实现 Drop 而引起问题。增加关键性测试可以避免这类问题。

```rust
#[test]
fn zeroize_on_struct() {
    parse_zeroize_test(stringify!(
        #[zeroize(drop)]
        struct Z {
            a: String,
            b: Vec<u8>,
            c: [u8; 3],
        }
    ));
}

#[test]
fn zeroize_on_enum() {
    parse_zeroize_test(stringify!(
        #[zeroize(drop)]
        enum Z {
            Variant1 { a: String, b: Vec<u8>, c: [u8; 3] },
        }
    ));
}
```

【反例】

在第三方库 [zeroize](https://github.com/iqlusioninc/crates/tree/main/zeroize) 中，曾经因为过程宏中对枚举类型没有实现 Drop 而引起问题。参见：[RUSTSEC-2021-0115](https://rustsec.org/advisories/RUSTSEC-2021-0115.html)

```rust
#[derive(Zeroize)]
#[zeroize(drop)]
pub enum Fails {
    Variant(Vec<u8>),
}

// This does compile with zeroize_derive version 1.1, meaning `#[zeroize(drop)]` didn't implement `Drop`.
impl Drop for Fails {
    fn drop(&mut self) {
        todo!()
    }
}
```

## P.MAC.Proc.03 保证过程宏的卫生性

【描述】

过程宏生成的代码尽量使用完全限定名，防止命名冲突产生意想不到的后果。 

【正例】

```rust
quote!(::std::ToString::to_string(a))
```

```rust
quote! {{
    use ::std::ToString;
    a.to_string()
}}
```

【反例】

```rust
quote!(a.to_string())
```

【测试】

使用`#![no_implicit_prelude]`属性来验证过程宏的卫生性。

```rust
#![no_implicit_prelude]

#[derive(MyMacro)]
struct A;
```

## P.MAC.Proc.04 给出正确的错误位置

【描述】

过程宏发生错误时，返回的错误应该有正确的位置信息。

【正例】

```rust
#[proc_macro_derive(MyMacro)]
pub fn derive_my_macro(input: TokenStream) -> TokenStream {
    let derive_input: DeriveInput = syn::parse_macro_input!(input as DeriveInput);

    if let Data::Enum(e) = &derive_input.data {
        for variant in &e.variants {
            if !variant.fields.is_empty() {
                // 使用variant的span
                return syn::Error::new_spanned(&variant, "must be a unit variable.")
                    .to_compile_error()
                    .into();
            }
        }
    }

    todo!()
}
```

【反例】

```rust
// 直接用Span::call_site()
Error::new(Span::call_site(), "requires unit variant")
    .to_compile_error()
    .into()
```

## P.MAC.Proc.05 代码生成要按情况选择使用过程宏还是`build.rs`

【描述】

用过程宏生进行代码生成，比如生成新类型或函数，有一个缺点就是：IDE无法识别它们，影响开发体验。

但是使用`build.rs`生成的代码，对 IDE 更友好。

不过随着 IDE 的增强，过程宏以后应该也能变得更加 IDE 友好。

建议按应用场景选择：

- `build.rs` 一般用于根据外部文件生成代码的场景。比如根据 `C` 头文件生成 Rust 绑定，或者根据 `proto` 文件生成相应的 Rust 类型等，供开发者直接使用。
- 过程宏一般用于消除样例式代码，提升库使用者的开发体验。

【正例】

`build.rs` 把 `tonic` 生成的代码直接放在 `src` 目录(生成的代码文件应该在 .gitignore 中忽略版本管理)，这样 IDE 能够识别它们使自动完成能够工作，提高开发效率。

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

## P.MAC.Proc.06 build.rs 生成的代码要保证没有任何警告

【描述】

build.rs 生成的代码(codegen)，要通过或忽略 clippy 检查，不要让用户/库的使用者自行忽略

codegen 库要保证生成的代码应该非常干净没有任何警告，不应该让库的使用者去处理生成代码中的警告

【正例】

tonic-build 生成的 rs 会通过 allow 忽略掉 clippy 警告

```rust
pub mod peer_communication_client {
    #![allow(unused_variables, dead_code, missing_docs, clippy::let_unit_value)]
    use tonic::codegen::*;
```

【反例】

lalrpop v0.19.6 生成的代码有几百个 clippy 警告，"淹没"了用户自己代码的 clippy 警告

```
warning: using `clone` on type `usize` which implements the `Copy` trait
      --> /home/w/temp/my_parser/target/debug/build/my_parser-dd96f436ee76c58d/out/my_parser.rs:182148:21
       |
182148 |         let __end = __start.clone();
       |                     ^^^^^^^^^^^^^^^ help: try removing the `clone` call: `__start`
```

使得 lalrpop 库的使用者必须手动给生成的模块代码加上 allow clippy ，给使用者带来不便

```rust
lalrpop_mod!(
    #[allow(clippy::all)]
    my_parser
);
```
