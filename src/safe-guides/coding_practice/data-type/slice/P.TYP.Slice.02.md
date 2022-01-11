## P.TYP.Slice.02 宜使用切片模式来提升代码的可读性

**【描述】**

切片也支持模式匹配，适当应用切片模式，可以有效提升代码可读性。

**【正例】**

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

