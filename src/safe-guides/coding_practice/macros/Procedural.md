# 过程宏

[过程宏（*Procedural macros*）](https://doc.rust-lang.org/reference/procedural-macros.html) 允许开发者来创建语法扩展。你可以通过过程宏创建类似函数的宏、派生宏以及属性宏。

过程宏必须被单独定义在一个类型为`proc-macro` 的 crate 中。

过程宏有两类报告错误的方式：`Panic` 或 通过 `compile_error`  宏调用发出错误。

过程宏不具有卫生性（hygiene），这意味着它会受到外部语法项（item）的影响，也会影响到外部导入。

过程宏可以在编译期执行任意代码。

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

