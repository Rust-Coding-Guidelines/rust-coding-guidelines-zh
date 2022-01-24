## G.NAM.06 遵循 `iter/ iter_mut/ into_iter` 规范来生成迭代器

### 【级别：建议】

### 【描述】

对于容纳 `U` 类型的容器 (container) ，其迭代器方法应该这样命名：

```rust,ignored
fn iter(&self) -> Iter             // Iter 实现 Iterator<Item = &U>
fn iter_mut(&mut self) -> IterMut  // IterMut 实现 Iterator<Item = &mut U>
fn into_iter(self) -> IntoIter     // IntoIter 实现 Iterator<Item = U>
```

此规则适用于在概念上属于同质集合的数据结构的方法，而非函数。例如，第三方库 `url`  中的 [percent_encode](https://docs.rs/url/1.4.0/url/percent_encoding/fn.percent_encode.html) 返回一个 URL 编码的字符串片段的迭代器。使用`iter/iter_mut/into_iter`约定的话，函数名就不会有任何明确的语义了。

### 【反例】

标准库中存在一个反例： `str` 类型是有效 UTF-8 字节的切片（slice），概念上与同质集合略有差别，所以 `str` 没有提供 `iter`/`iter_mut`/`into_iter` 命名的迭代器方法，而是提供 [`str::bytes`] 方法来输出字节迭代器、[`str::chars`] 方法来输出字符迭代器。

[`str::bytes`]: https://doc.rust-lang.org/std/primitive.str.html#method.bytes
[`str::chars`]: https://doc.rust-lang.org/std/primitive.str.html#method.chars

### 【正例】

- [`Vec::iter`](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.iter)
- [`Vec::iter_mut`](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.iter_mut)
- [`Vec::into_iter`](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.into_iter)
- [`BTreeMap::iter`](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html#method.iter)
- [`BTreeMap::iter_mut`](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html#method.iter_mut)


### 【参考】

参考 [RFC 199] 。

[RFC 199]: https://github.com/rust-lang/rfcs/blob/master/text/0199-ownership-variants.md


### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group |是否可定制 |
| ------ | ---- | --------- | ------ | ------ |
|  _ | no | no | _ | yes |

【定制化参考】

检测 `iter/iter_mut/into_iter` 方法的返回类型是否对应 `Iter/IterMut/IntoIter` ，如果不是，则给予警告。
