## P.MAC.PRO.01 不要使用过程宏来规避静态分析检查

**【描述】**

不要利用过程宏来定义能规避 Rust 静态分析检查的宏。

**【反例】**

在 Rust 生态中有一个库 [`plutonium`](https://github.com/mxxo/plutonium)，该库利用了过程宏来消除代码中直接的 `unsafe` 块的使用，从而规避了编译器对 `unsafe` 关键字的静态检查。

该库会通过`#[safe]` 过程宏在自动生成代码的时候为函数体添加 `unsafe` 块，但这会影响到 `unsafe` 调用链依赖静态检查传播，从而进一步打断 unsafe 调用链路，影响后续通过 `unsafe` 关键字来定位问题。

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
   
**【正例】**

对于不安全的函数，应该显式地使用 `unsafe` 。这样做的好处是利用 Rust 编译器静态检查传播 unsafe 调用链条，以达到可以全局查找 unsafe 使用来消除一些代码隐患，方便定位问题。

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


**【相关讨论】**

- [RUSTSEC-2020-0011](https://rustsec.org/advisories/RUSTSEC-2020-0011.html)
- [https://github.com/RustSec/advisory-db/issues/275](https://github.com/RustSec/advisory-db/issues/275)
- [https://github.com/rust-lang/unsafe-code-guidelines/issues/278](https://github.com/rust-lang/unsafe-code-guidelines/issues/278)
