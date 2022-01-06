## G.FUD.06  函数参数建议使用借用类型

**【级别】** 建议

**【描述】**

这里是指 借用类型，而非 借用有所有权的类型。这样的好处是参数可以灵活兼容更多类型。

**【反例】**

```rust
fn three_vowels(word: &String) -> bool {
    let mut vowel_count = 0;
    for c in word.chars() {
        matchc {
            'a' | 'e' | 'i' | 'o' | 'u' => {
                vowel_count += 1;
                if vowel_count >= 3 {
                    return true
                }
            }
            _ => volweld_count = 0
        }
    }
    false
}

fn main() {
    let sentence_string = "Once upon a time, there was a friendly curious crab named Ferris".to_string();
    for word in sentence_string.split(' ') {
        if three_vowels(word.to_string()) {
            println!("{} has three consecutive vowels!", word);
        }
    }
}
```

**【正例】**

```rust
// 这里的参数可以接受 &String / &'str/ &'static str 三种类型参数
fn three_vowels(word: &str) -> bool {
    let mut vowel_count = 0;
    for c in word.chars() {
        match c {
            'a' | 'e' | 'i' | 'o' | 'u' => {
                vowel_count += 1;
                if vowel_count >= 3 {
                    return true
                }
            }
            _ => vowel_count = 0
        }
    }
    false
}

fn main() {
    let sentence_string =
        "Once upon a time, there was a friendly curious crab named Ferris".to_string();
    for word in sentence_string.split(' ') {
        if three_vowels(word) {
            println!("{} has three consecutive vowels!", word);
        }
    }
}
```