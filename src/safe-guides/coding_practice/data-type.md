# 数据类型

数据类型记录 Rust 标准库提供的 原生类型，以及结构体和枚举体等编码实践。

- [单元类型](data-type/unit.md)
- [布尔](data-type/bool.md)
- [字符](data-type/char.md)
- [整数](data-type/int.md)
- [浮点数](data-type/float.md)
- [切片](data-type/slice-and-str.md)
- [元组](data-type/tuple.md)
- [数组](data-type/array.md)
- [结构体](data-type/struct.md)
- [枚举](data-type/enum.md)

---

## P.TYP.01 类型转换要尽量使用安全的方式

Rust 中的类型转换有多种方式，包括 `as` 强转、`From/Into`安全转换函数、`Deref`、以及 Unsafe 的 `std::mem::transmute` 等。在使用类型转换的时候，要注意场景，选择合适的方式和安全条件，不要让转换产生未定义行为。



---

## G.TYP.01 类型转换尽可能使用安全的转换函数代替 `as`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [as_conversions](https://rust-lang.github.io/rust-clippy/master/#as_conversions) | yes| no | restriction | allow |
| [cast_lossless](https://rust-lang.github.io/rust-clippy/master/#cast_lossless) | yes| no | pedantic | allow |
| [cast_possible_truncation](https://rust-lang.github.io/rust-clippy/master/#cast_possible_truncation) | yes| no | pedantic | allow |
| [cast_possible_wrap](https://rust-lang.github.io/rust-clippy/master/#cast_possible_wrap) | yes| no | pedantic | allow |
| [cast_precision_loss](https://rust-lang.github.io/rust-clippy/master/#cast_precision_loss) | yes| no | pedantic | allow |
| [cast_sign_loss](https://rust-lang.github.io/rust-clippy/master/#cast_sign_loss) | yes| no | pedantic | allow |
| [fn_to_numeric_cast](https://rust-lang.github.io/rust-clippy/master/#fn_to_numeric_cast) | yes| no | Style | warn |
| [fn_to_numeric_cast_with_truncation](https://rust-lang.github.io/rust-clippy/master/#fn_to_numeric_cast_with_truncation) | yes| no | Style | warn |
| [char_lit_as_u8](https://rust-lang.github.io/rust-clippy/master/#char_lit_as_u8) | yes| no | Complexity | warn |
| [cast_ref_to_mut](https://rust-lang.github.io/rust-clippy/master/#cast_ref_to_mut) | yes| no | correctness | deny |
| [ptr_as_ptr](https://rust-lang.github.io/rust-clippy/master/#ptr_as_ptr) | yes| no | pedantic | allow |

### 【描述】

Rust 的 `as` 转换包含了「静默的有损转换(lossy conversion)」。诸如 `i32::from` 之类的转换函数只会执行无损转换(lossless conversion)。 如果输入表达式的类型发生变化，使用转换函数可以防止转换变成无声的有损转换，并使阅读代码的人更容易知道转换是无损的。
