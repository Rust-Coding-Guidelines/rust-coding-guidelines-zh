# 数据类型

数据类型记录 Rust 标准库提供的 原生类型，以及结构体和枚举体等编码实践。

- [单元类型](data-type/unit.md)
- [布尔](data-type/bool.md)
- [字符](data-type/char.md)
- [整数](data-type/int.md)
- [浮点数](data-type/float.md)
- [引用](data-type/ref.md)
- [切片](data-type/slice-and-str.md)
- [元组](data-type/tuple.md)
- [固定长度数组](data-type/array.md)
- [动态数组](data-type/vec.md)
- [结构体](data-type/struct.md)
- [枚举](data-type/enum.md)

---

## P.TYP.01 类型转换要尽量使用安全的方式

**【描述】**

Rust 中的类型转换有多种方式，包括 `as` 强转、`From/Into`安全转换函数、`Deref`、以及 Unsafe 的 `std::mem::transmute` 等。在使用类型转换的时候，要注意场景，选择合适的方式和安全条件，不要让转换产生未定义行为。

## P.TYP.02  对数组和集合容器进行索引要使用 `usize` 类型

**【描述】**

Rust 中只允许索引为 `usize` 类型，因为：

1. 负索引是无意义的。
2. `usize`和 裸指针大小相同，意味着指针算法不会有任何隐藏的强制转换
3. `std::mem::size_of()` 和 `std::mem::align_of()` 的函数返回 `usize` 类型。
4. `usize` 不会因为平台架构的切换而导致索引值被截断的问题，比如 将`u32`类型的索引 用到 16位大小的嵌入式平台就会出问题。



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

## G.TYP.02  数字字面量在使用的时候应该明确标注好类型

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [default_numeric_fallback](https://rust-lang.github.io/rust-clippy/master/#default_numeric_fallback) | yes           | no           | restriction | allow |

### 【描述】

如果数字字面量没有被指定具体类型，那么单靠类型推导，整数类型会被默认绑定为 `i32` 类型，而浮点数则默认绑定为 `f64`类型。这可能导致某些运行时的意外。

【正例】

```rust
let i = 10u32;
let f = 1.23f32;
```

【反例】

```rust
let i = 10; // i32
let f = 1.23; // f64
```

