## P.STR.03    可以使用`Cow<str>`来代替直接使用字符串，它可以减少Copy

**【描述】**

使用 `Cow<str>` 作为字符串处理函数参数和返回值，可以尽可能地减少数据Copy 和 内存分配。当字符串没有修改的时候，实际使用的是 `&'a str`，只有当数据修改的时候才会使用`String`。对于读操作大于写操作的场景，使用 `Cow<str>` 比较合适。

**【示例】**

```rust
// 对输入的字符串进行转义
pub fn naive<'a, S: Into<Cow<'a, str>>>(input: S) -> Cow<'a, str> {
    let input = input.into();
    fn is_trouble(c: char) -> bool {
        c == '<' || c == '>' || c == '&'
    }

    if input.contains(is_trouble) {
        let mut output = String::with_capacity(input.len());
        for c in input.chars() {
            match c {
                '<' => output.push_str("&lt;"),
                '>' => output.push_str("&gt;"),
                '&' => output.push_str("&amp;"),
                _ => output.push(c)
            }
        }
        // 只有在字符串修改的时候才使用 String
        Cow::Owned(output)
    } else {
        //其他情况使用 &str
        input
    }
}
```



