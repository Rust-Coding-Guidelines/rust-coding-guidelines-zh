# 切片

切片（slice）允许开发者引用集合中连续的元素序列，类型签名用 `[T]`表示，但因为它是动态大小类型（DST），所以一般用 `&[T]` 表示切片。

`&str` 就是一种字符串切片。

---
<!-- toc -->
---

## P.TYP.Slice.01  宜使用切片迭代器来代替手工索引

**【描述】**

在 for 循环中使用索引是比较常见的编程习惯，但是这种方式是最有可能导致边界错误的。

利用 切片自带的方法，并利用迭代器，可以避免这种错误。

**【正例】**

```rust
let points: Vec<Coordinate> = ...;
let mut differences = Vec::new();

// 切片提供 windows 或 array_windows 方法返回迭代器
for [previous, current] in points.array_windows().copied() {
    differences.push(current - previous);
}
```

**【反例】**

```rust
let points: Vec<Coordinate> = ...;
let differences = Vec::new();

// 人工计算长度选择范围很可能会出错
for i in 1..points.len() [
  let current = points[i];
  let previous = points[i-1];
  differences.push(current - previous);
]
```

## P.TYP.Slice.02 宜使用切片模式来提升代码的可读性

**【描述】**

切片也支持模式匹配，适当应用切片模式，可以有效提升代码可读性。

【示例】

利用切片模式编写判断回文字符串的函数。代码来自于：[Daily Rust: Slice Patterns](https://adventures.michaelfbryan.com/posts/daily/slice-patterns/#matching-the-start-of-a-slice)  ，还有更多用例。

```rust
pub fn word_is_palindrome(word: &str) -> bool {
    let letters: Vec<_> = word.chars().collect();

    is_palindrome(&letters)
}
// 利用切片模式匹配来判断是否回文字符串
fn is_palindrome(items: &[char]) -> bool {
    match items {
        [first, middle @ .., last] => first == last && is_palindrome(middle),
        [] | [_] => true,
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn known_palindromes() {
        assert!(word_is_palindrome(""));
        assert!(word_is_palindrome("a"));
        assert!(word_is_palindrome("aba"));
        assert!(word_is_palindrome("abba"));
    }

    #[test]
    fn not_palindromes() {
        assert!(!word_is_palindrome("abc"));
        assert!(!word_is_palindrome("abab"));
    }
}

```

