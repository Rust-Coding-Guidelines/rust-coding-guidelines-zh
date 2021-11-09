# 元组

元组是异构复合类型，可以存储多个不同的值。

---

## P.TYP.Tuple.01  可以使用元组解构来同时定义多个变量

【描述】

可以利用元组解构的特性，来更好地精简代码。

【正例】

```rust
struct A(i32, i32);

fn hello( A(a, b): A){
    println!("{}, {}", a, b);
}

fn main(){
    let a = A(1, 2) ;
    hello(a);
}
```

## G.TYP.Tuple.01  使用元组时，其元素最多不要超过3个

### 【级别：建议】

建议按此规范执行。

【描述】

元组是异构复合类型，元素过多，其表达力会下降，影响代码可读性和可维护性。

尤其是利用元组作为函数返回值时，不宜过多。

【反例】

```rust
fn convert(x: i8) -> (i8, i16, i32, i64, f32, f64) {
    (x as i8, x as i16, x as i32, 
     x as i64, x as f32, x as f64)
}

fn main(){
    let _ = convert(3);
}
```
