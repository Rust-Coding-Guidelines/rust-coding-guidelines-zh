# 字符

在 Rust 中，字符是一个合法的 Unicode 标量值（Unicode scalar value），一个字符大小为 4 字节，对应一个 Unicode 码位（CodePoint）。

## 列表


- [G.TYP.CHR.01 不宜将字符字面量强制转换为 `u8`](./char/G.TYP.CHR.01.md)
- [G.TYP.CHR.02 字符串方法中如果需要单个字符的值作为参数，宜使用字符而非字符串](./char/G.TYP.CHR.02.md)
- [G.TYP.CHR.03 当需要将整数转换为字符时，应使用安全转换函数，而非 transmute](./char/G.TYP.CHR.03.md)