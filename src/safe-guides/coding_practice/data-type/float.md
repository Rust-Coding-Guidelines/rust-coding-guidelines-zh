# 浮点数

Rust 的浮点数包括 `f32` 和 `f64` 两种类型。Rust 编译器默认推断的 Float 类型是 `f64`。

## 列表

- [G.TYP.FLT.01 使用浮点数字面量时，要警惕是否存在被Rust编译器截断的风险](./float/G.TYP.FLT.01.md)
- [G.TYP.FLT.02 从任何数字类型转换为`f64`类型时注意避免损失精度](./float/G.TYP.FLT.02.md)
- [G.TYP.FLT.03 对精度高要求的场景下，使用浮点数进行运算和比较时需要注意精度损失](./float/G.TYP.FLT.03.md)
- [G.TYP.FLT.04 宜使用Rust内置方法处理浮点数计算](./float/G.TYP.FLT.04.md)
- [G.TYP.FLT.05 禁止在浮点数和整数相互转换时使用 transmute](./float/G.TYP.FLT.05.md)