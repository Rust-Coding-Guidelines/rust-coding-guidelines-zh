## G.VAR.02  不应使用非 ASCII 字符作为标识符

**【级别】** 要求

**【描述】**

Rust 语言默认支持 Non ASCII 字符作为合法标识符。但是，为了统一团队代码风格，建议使用最常用的 ASCII 字符作为合法标识符。

> 此外，通常**命名相关**的 Clippy Lint 检查只支持英文命名。

**【反例】**

```rust
// 不符合
#[derive(Debug)]
struct 人 {
    /// 普通话
    名字: String,
    /// 廣東話
    屋企: String,
}

fn main () {
    let 我的名字 = "मनीष".to_string();
    let 我嘅屋企 = "Berkeley".to_string();
    
    // मराठी
    let मनीष = 人 {
        名字: 我的名字,
        屋企: 我嘅屋企,
    };
    
    // हिंदी
    let उसका_नाम = "مصطفى".to_string();
    let 他的家 = "Oakland".to_string();
   
    // اردو 
    let مصطفى = 人 {
        名字: उसका_नाम,
        屋企: 他的家,
    }; 
    
    println!("मी: {:?}", मनीष);
    println!("माझा मित्र: {:?}", مصطفى);
}

// 输出：
// मी: 人 { 名字: "मनीष", 屋企: "Berkeley" }
// माझा मित्र: 人 { 名字: "مصطفى", 屋企: "Oakland" }
```

**【正例】**

```rust
// 符合
#[derive(Debug)]
struct People {
    name: String,
    addr: String,
}

fn main () {
    let name = "मनीष".to_string();
    let addr = "Berkeley".to_string();
    
    // मराठी
    let me = People {
        name: name,
        addr: addr,
    };
    
    // हिंदी
    let name = "مصطفى".to_string();
    let addr = "Oakland".to_string();
   
    // اردو     
    let he = People {
        name: name,
        addr: addr,
    }; 
    
    println!("my name: {:?}", me);
    println!("his name: {:?}", he);
}

// 输出
// my name: People { name: "मनीष", addr: "Berkeley" }
// his name: People { name: "مصطفى", addr: "Oakland" }
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [rustc-lint: non-ascii-idents](https://doc.rust-lang.org/rustc/lints/listing/allowed-by-default.html#non-ascii-idents) | no            | yes          | pedantic   | allow |
