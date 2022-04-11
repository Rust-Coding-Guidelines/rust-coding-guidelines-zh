## G.TYP.FLT.02  从任何数字类型转换为浮点类型时注意避免损失精度

**【级别】** 建议

**【描述】**

开发者了解发生精度损失的位置，会对解决因为转换而损失精度的问题更加有好处。

**【反例】**

```rust
#![warn(clippy::cast_precision_loss)]

fn main(){
    // 不符合
    let x = u64::MAX;
    x as f64;
    // 不符合
    let x: f32 = 16_777_219.0 ; // 该数字转换为 f64 后会表示为 16_777_220.0
    x as f64;
}
```

**【正例】**

```rust
#![warn(clippy::cast_precision_loss)]

fn main(){
    // 符合
    let x = i32::MAX;
    let y = f64::from(x);  // 如果 x 为 u64 类型，则编译会出错，不接受这类转换
    // 符合
    let x: f32 = 16_777_219.0 ;
    let y = f64::from(x);  // 该数字转换为 f32 后会表示为 16_777_220.0
    println!("{y:?}")
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [cast_precision_loss](https://rust-lang.github.io/rust-clippy/master/#cast_precision_loss) | yes           | no           | pedantic   | allow |


