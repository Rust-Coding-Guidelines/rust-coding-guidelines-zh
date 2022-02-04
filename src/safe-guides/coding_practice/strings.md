# 字符串

Rust 中字符串是有效的 UTF-8 编码的字节数组。

Rust 字符串类型众多，但本节内容主要围绕 ：`String` / `&str`  

## 列表

- [P.STR.01 处理字符串非必要不要按字符来处理，应该按字节处理](./strings/P.STR.01.md)
- [P.STR.02 创建字符串时，可以预先分配大约足够的容量来避免后续操作中产生多次分配](./strings/P.STR.02.md)
- [P.STR.03 可以使用`Cow<str>`来代替直接使用字符串，它可以减少Copy](./strings/P.STR.03.md)
- [P.STR.04 在使用内建字符串处理函数或方法的时候，应该注意避免隐藏的嵌套迭代或多次迭代](./strings/P.STR.04.md)
- [P.STR.05 只有在合适的场景下，才使用第三方库正则表达式regex](./strings/P.STR.05.md)
- [P.STR.06 在拼接字符串时，建议使用format!](./strings/P.STR.06.md)
- [G.STR.01 在实现Display特质时不要调用to_string()方法](./strings/G.STR.01.md)
- [G.STR.02 在追加字符串时使用push_str方法可读性更强](./strings/G.STR.02.md)
- [G.STR.03 将只包含 ASCII字符的字符串字面量转为字节序列可以直接使用b"str" 语法代替调用as_bytes方法](./strings/G.STR.03.md)
- [G.STR.04 需要判断字符串以哪个字符开头或结尾时，不要按字符迭代比较](./strings/G.STR.04.md)
- [G.STR.05 对字符串按指定位置进行切片的时候需要小心破坏其 UTF-8 编码](./strings/G.STR.05.md)