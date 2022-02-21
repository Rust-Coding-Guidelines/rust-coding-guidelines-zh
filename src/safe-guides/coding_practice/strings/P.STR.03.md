## P.STR.03 在使用内建字符串处理函数或方法的时候，应注意避免隐藏的嵌套迭代或多次迭代

**【描述】**

比如 `contains` 函数的实现就是按字符遍历字符串，但是如果你将它用于一个字符串的迭代处理中，就会产生嵌套迭代，时间复杂度从你以为的 `O(n)` 变成了 `O(n^2)`。没有将其用于迭代中，也有可能产生多次迭代，`O(n)` 变为 `O(n+m)` 。 为了避免这个问题，我们可以用 `find`  来代替 `contains`。

所以，在使用内建函数的时候要注意它的实现，选择合适的函数或方法，来避免这类问题。

**【示例】**

```rust
// 对输入的字符串进行转义
pub fn find<'a, S: Into<Cow<'a, str>>>(input: S) -> Cow<'a, str> {
    let input = input.into();
    fn is_trouble(c: char) -> bool {
        c == '<' || c == '>' || c == '&'
    }
    
    // 使用 find 而非 contains
    // find 使用模式查找，可以返回匹配字符的位置信息
    let first = input.find(is_trouble);
    
    // 利用 find 的位置信息，避免第二次遍历
    if let Some(first) = first {
        let mut output = String::from(&input[0..first]);
        output.reserve(input.len() - first);
        let rest = input[first..].chars();
        for c in rest {
            match c {
                '<' => output.push_str("&lt;"),
                '>' => output.push_str("&gt;"),
                '&' => output.push_str("&amp;"),
                _ => output.push(c),
            }
        }

        Cow::Owned(output)
    } else {
        input.into()
    }
}
```



