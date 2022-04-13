## G.TYP.TUP.01  使用元组时，其元素不宜超过3个

**【级别】** 建议

**【描述】**

元组是异构复合类型，元素过多，其表达力会下降，影响代码可读性和可维护性。

尤其是利用元组作为函数返回值时，不宜过多。

**【反例】**

```rust
// 不符合：超过3个元组参数
fn convert(x: i8) -> (i8, i16, i32, i64, f32, f64) {
    (x as i8, x as i16, x as i32, 
     x as i64, x as f32, x as f64)
}

fn main(){
    let _ = convert(3);
}
```

