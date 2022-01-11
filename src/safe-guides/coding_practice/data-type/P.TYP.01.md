## P.TYP.01 类型转换要尽量使用安全的方式

**【描述】**

Rust 中的类型转换有多种方式，其中 `as` 强转、Unsafe 的 `std::mem::transmute` 为不安全转换。`From/Into`安全转换函数为安全转换。在使用类型转换时，应优先使用安全转换方式。

**【反例】**
```rust
// 产生数据截断
let a = i32::MAX;
let b = a as u16; 
println!("{}, {}", a, b); // 2147483647, 65535

// 产生精度损失
let a = std::f64::consts::PI;
let b = a as f32; 
println!("{}, {}", a, b); // 3.141592653589793, 3.1415927

// 结果不正确
let a: f64 = 123456.0;
let b: i64 = unsafe {
std::mem::transmute(a)
};
println!("{}, {}", a, b); // 123456, 4683220244930494464
```

**【正例】**
```rust
let a: f32 = 123456.0; 
let b: f64 = a.try_into().expect("trans failed"); 
println!("{}, {}", a, b); // 123456, 123456
```
