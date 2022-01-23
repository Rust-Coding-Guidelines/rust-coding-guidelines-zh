## G.CNS.03 不宜将量大的数据结构定义为常量

**【级别】** 建议

**【描述】**

因为[常量会到处内联](https://doc.rust-lang.org/reference/items/constant-items.html#constant-items)，即复制到各个使用到它的地方。而静态变量不会内联，它是全局的且有一个引用地址。
所以当要创建一个很大的常量数组时，应该考虑将其换成静态变量以提高程序运行效率。（详情可见：[const-vs-static](https://rust-lang.github.io/rfcs/0246-const-vs-static.html#motivation)）

相关：[G.TYP.Array.01 ](./data-type/array.md)

**【反例】**

```rust
fn main() {
    const MONTHS: [&str; 12] = ["January", "Feburary", "March", "April",
                                "May", "June", "July", "August",
                                "September", "October", "November", "December"];
}
```

**【正例】**

```rust
fn main() {
    static MONTHS: [&str; 12] = ["January", "Feburary", "March", "April",
                                "May", "June", "July", "August",
                                "September", "October", "November", "December"];
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| _ | no           | no           | _   | yes |

**【定制化参考】**

这条规则如果需要定制Lint，则需要找出每个定义的常量再判断其空间占用，或可直接排除基础类型以外的数据类型。

