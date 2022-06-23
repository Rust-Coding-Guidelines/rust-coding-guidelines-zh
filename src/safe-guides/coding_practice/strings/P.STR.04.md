## P.STR.04 在使用 `Cow<'a, B> `时要注意选择合理场景以便最大化地优化性能

**【描述】**

 `Cow<'a, B>` 可以减少不必要的内存拷贝。Cow 代表 Clone-On-Write，意味着，使用它可以只在必要的时候再进行拷贝。

 但它并不是万能的，只有在需要大量读取数据但仅有少量情况需要修改时，`Cow<'a, B>` 才能真正起到优化性能的作用。

 如果不在意依赖库过多，编译文件更大，也可以使用第三方库 `regex` 来处理大数据的搜索匹配和替换等需求，性能更佳。


**【反例】**

假设场景一：要处理的大文件中，至少一半数据包含转义符号

```rust
// 不符合：这种情况在输入不包含转义符号的数据时，也需要对所有字符进行匹配处理，性能较低
pub fn naive(input: &str) -> String {
    let mut output = String::new();
    for c in input.chars() {
        match c {
            '<' => output.push_str("&lt;"),
            '>' => output.push_str("&gt;"),
            '&' => output.push_str("&amp;"),
            _ => output.push(c)
        }
    }
    output
}
```

假设场景二：要处理的大文件中，几乎所有数据都包含转义符号。这种场景下，使用 `Cow<'a, B>` 对性能也无法起到太大的优化作用，此时宜尝试使用 `regex` 之类的高性能第三方库处理。

**【正例】**

假设场景一：要处理的大文件中，至少一半数据包含转义符号。

```rust
// 对输入的字符串进行转义
// 符合： 性能提升大概 1 倍
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