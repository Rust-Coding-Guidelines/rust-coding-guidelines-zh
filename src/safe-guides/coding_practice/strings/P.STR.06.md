## P.STR.06    在拼接字符串时，建议使用`format!`

**【描述】**

在Rust中有很多方法可以连接字符串，不同的连接方法适用于不同的场景，性能也会有所差别。

**【示例】**

```rust
 // 组合字符串是最简单和直观的方法，尤其是在字符串和非字符串混合的情况下。
 fn main() {
	let name = "world!";
	let hw = format!("Hello {}!", name);
	println!("{:#?}", hw);
 }
 
 // 在追加字符串的时候，可以使用`push_str`方法,`push_str`性能比`format!`更好
 fn main() {
    let mut hw = String::new();
    hw.push_str("hello");
    hw.push_str(" world!");
    println!("{:#?}", hw);
}

 // 通过`concat()`方法将字符串数组拼接成一个字符串
 fn main() {
    let hw = ["hello", " ", "world!"].concat();
    println!("{:#?}", hw);
 }
 
 // 通过`join()`方法将字符串数组拼接成一个字符串
 fn main() {
    let hw_1 = ["hello", "world!"].join("");
    println!("{:#?}", hw_1);
	// 输出：
	// helloworld!
	
    // 使用`join()`方法在拼接字符串时添加或指定字符
    let hw_2 = ["hello", "world!"].join("+");
    println!("{:#?}", hw_2);
	// 输出：
	// hello+world!
 }
 
 // 使用`collect()`方式对数组中的字符串进行拼接
 fn main() {
    let hw = ["hello", " ", "world!"];
    let res: String = hw.iter().map(|x| *x).collect();
    println!("{:#?}", res);
 }
 
 // 使用符号`+`进行字符串拼接
 fn main() {
    let hw_1 = &(String::from("hello") + &String::from(" ") + &String::from("world!"));
    println!("{:#?}", hw_1);
    
    let hw_2 = &(String::from("hello") + " " + "world!");
    println!("{:#?}", hw_2);
    
    let hw_3 = &("hello".to_owned() + " " + "world!");
    println!("{:#?}", hw_3);

    let hw_4 = &("hello".to_string() + " " + "world!");
    println!("{:#?}", hw_4);
 }
```


