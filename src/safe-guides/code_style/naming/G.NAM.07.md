## G.NAM.07 迭代器类型名称应该与产生它们的方法相匹配

**【级别】** 建议

**【描述】**

一个叫做`into_iter()`的方法应该返回一个叫做`IntoIter`的类型，同样，所有其他返回迭代器的方法也是如此。

这条规则主要适用于方法，但通常对函数也有意义。例如，第三方库 `url`  中的 [percent_encode](https://docs.rs/url/1.4.0/url/percent_encoding/fn.percent_encode.html) 返回一个`PercentEncode` 类型的迭代器。

**【正例】**

来自标准库的例子：

* [`Vec::iter`] 返回 [`Iter`][slice::Iter]
* [`Vec::iter_mut`] 返回 [`IterMut`][slice::IterMut]
* [`Vec::into_iter`] 返回 [`IntoIter`][vec::IntoIter]
* [`BTreeMap::keys`] 返回 [`Keys`][btree_map::Keys]
* [`BTreeMap::values`] 返回 [`Values`][btree_map::Values]

[`Vec::iter`]: https://doc.rust-lang.org/std/vec/struct.Vec.html#method.iter
[slice::Iter]: https://doc.rust-lang.org/std/slice/struct.Iter.html
[`Vec::iter_mut`]: https://doc.rust-lang.org/std/vec/struct.Vec.html#method.iter_mut
[slice::IterMut]: https://doc.rust-lang.org/std/slice/struct.IterMut.html
[`Vec::into_iter`]: https://doc.rust-lang.org/std/vec/struct.Vec.html#method.into_iter
[vec::IntoIter]: https://doc.rust-lang.org/std/vec/struct.IntoIter.html
[`BTreeMap::keys`]: https://doc.rust-lang.org/std/collections/struct.BTreeMap.html#method.keys
[btree_map::Keys]: https://doc.rust-lang.org/std/collections/btree_map/struct.Keys.html
[`BTreeMap::values`]: https://doc.rust-lang.org/std/collections/struct.BTreeMap.html#method.values
[btree_map::Values]: https://doc.rust-lang.org/std/collections/btree_map/struct.Values.html


**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------ | ---- | --------- | ------ | ------ | 
|  _ | no | no | _ | yes |


【定制化参考】

检测返回迭代器的方法，其返回类型应该与方法名相匹配，否则给予警告。
