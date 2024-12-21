## G.STR.05   对字符串按指定位置进行切片的时候需要小心破坏其 UTF-8 编码

**【级别】** 建议

**【描述】**

字符串默认是合法的 `UTF-8`字节序列，如果通过指定索引位置来对字符串进行切片，有可能破坏其合法 `UTF-8` 编码，除非这个位置是确定的，比如按 `char_indices` 方法来定位是合法的。

**【反例】**

```rust
#![warn(clippy::string_slice)]

fn main(){ 
    let s = "Ölkanne";
    // 不符合
    // 字节索引 1 不是字符的边界，所以程序会 panic 
    // `Ölkanne` 的 'Ö' 是 字节 `0..2`
    let sub_s = &s[1..];
    // println!("{:?}", sub_s);
}

```

**【正例】**

```rust
#![allow(clippy::string_slice)]

fn main(){
    let s = "Ölkanne";
    let mut char_indices = s.char_indices();
    assert_eq!(Some((0, 'Ö')), char_indices.next());
    // assert_eq!(Some((2, 'l')), char_indices.next()); 
    let pos = if let Some((pos, _)) = char_indices.next(){ pos } else {0};
    // 符合：计算出了正确的字符位置
    // 注意，这里 lint 检查工具可能误报，但这里是合法的，所以将lint设置为 allow
    let sub_s = &s[pos..];
    assert_eq!("lkanne", sub_s);
}
```

**【Lint 检测】**

| lint name                                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | 默认level |
| ---------------------------------------------------------------------------- | ------------- | ------------ | ----------- | --------- |
| [string_slice](https://rust-lang.github.io/rust-clippy/master/#string_slice) | yes           | no           | restriction | allow     |


