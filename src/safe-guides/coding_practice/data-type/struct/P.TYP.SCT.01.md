## P.TYP.SCT.01 为结构体实现构造性方法时，避免构造后再初始化的情况

**【描述】**

跟其他OOP 或 FP 语言不一样， Rust 的惯用方式是构建即初始化。

**【反例】**

```rust
// 不符合
// 先构建
let mut dict = Dictionary::new();
// 后初始化
dict.load_from_file("./words.txt")?;
```

**【正例】**

```rust
// 符合
// 构建即初始化
let dict = Dictionary::from_file("./words.txt")?;

impl Dictionary {
  fn from_file(filename: impl AsRef<Path>) -> Result<Self, Error> {
    let text = std::fs::read_to_string(filename)?;
    // 不会去存储空状态
    let mut words = Vec::new();
    for line in text.lines() {
      words.push(line);
    }
    Ok(Dictionary { words })
  }
}
```


