# 变量

这里所说变量是指局部变量。默认情况下，Rust 会强制初始化所有值，以防止使用未初始化的内存。

变量命名风格指南请看 [编码风格-命名](../code_style/naming.md)

---
<!-- toc -->
---

## P.VAR.01 非必要不要像 C 语言那样先声明可变变量然后再去赋值

**【描述】**

不要先声明一个可变的变量，然后再后续过程中去改变它的值。一般情况下，声明一个变量的时候，要对其进行初始化。如果后续可能会改变其值，要考虑优先使用变量遮蔽（继承式可变）功能。如果需要在一个子作用域内改变其值，再使用可变绑定或可变引用。

## P.VAR.02 禁止将局部变量的引用返回函数外

**【描述】**

局部变量生命周期始于其声明终于其作用域结束。如果在其生命周期之外被引用，则程序的行为是未定义的。当然，Rust 编译器也会阻止你这么干。

## P.VAR.03   利用变量遮蔽功能保证变量安全使用

**【描述】**

在某些场景，可能会临时准备或处理一些数据，但在此之后，数据只用于检查而非修改。

那么可以将其通过变量遮蔽功能，重写绑定为不可变变量，来表明这种 临时可变，但后面不变的意图。

【正例】

```rust
let mut data = get_vec();
data.sort(); // 临时需要排序
let data = data; //  后面就不需要改动了，由编译器可以确保

// Here `data` is immutable.
```

【反例】

```rust
let data = { 
    let mut data = get_vec();
    data.sort();
    data // 虽然后面不再改动，但代码语义上没有表现出来先改变，后不变那种顺序语义
};

// Here `data` is immutable.
```



---

## G.VAR.01 交换两个变量的值应该使用 `std::mem::swap` 而非赋值

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [almost_swapped](https://rust-lang.github.io/rust-clippy/master/#almost_swapped) | yes| no | Correctness | deny |

### 【描述】

【正例】

```rust
let mut a = 1;
let mut b = 2;
std::mem::swap(&mut a, &mut b);
```

【反例】

```rust
let mut a = 1;
let mut b = 2;
a = b;
b = a;  
```



## G.VAR.02   使用解构元组方式定义多个变量时不要使用太多单个字符来命名变量

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [many_single_char_names](https://rust-lang.github.io/rust-clippy/master/#many_single_char_names) | yes           | no           | pedantic   | allow |

### 【描述】

在解构元组的方式定义多个变量时，有时候变量命名可能是无特别语义的，比如临时值，可以用简单的单个字符来定义变量名，但是不宜太多。

该 lint 对应 `clippy.toml` 配置项：

```toml
# 修改可以绑定的单个字符变量名最大数量。默认为 4
single-char-binding-names-threshold=4
```

【正例】

超过四个的，就需要起带语义的命名。

```rust
let (a, b, c, d) = (...);
let (width, high, len, shape, color, status) = (...);
```

【反例】

```rust
let (a, b, c, d, e, f, g) = (...);
```



## G.VAR.03   通常不要使用非 ASCII 字符作为标识符

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [rustc-lint: non-ascii-idents](https://doc.rust-lang.org/rustc/lints/listing/allowed-by-default.html#non-ascii-idents) | no            | yes          | pedantic   | allow |

### 【描述】

Rust 语言默认支持 Non ASCII 字符作为合法标识符。但是，为了统一团队代码风格，建议使用最常用的 ASCII 字符作为合法标识符。

另外，只有使用英文的命名才能让**命名相关**的 Lint 生效。

【正例】

```rust
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

【反例】

```rust
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

## G.VAR.04 在子作用域中使用变量遮蔽功能要小心

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| _ | no           | no           | _   | yes |

【定制化参考】
这条规则如果需要定制Lint，则可以分别扫描当前作用域和子作用域中的变量，判断是否存在同名问题。

**【描述】**

当两个作用域存在包含关系时，使用变量遮蔽功能要小心，防止引起逻辑Bug。

**【示例】**
【反例】
```rust
fn main(){
    let mut a = 0;
    {
        // do something
        a = 42;
        
        // bug
        // let a = 42;
    }
    
    a; // use a again
}
```

## G.VAR.05  不要在当前作用域使用变量遮蔽功能

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [shadow_reuse](https://rust-lang.github.io/rust-clippy/master/#shadow_reuse) | yes           | no           | restriction   | allow |
| [shadow_same](https://rust-lang.github.io/rust-clippy/master/#shadow_same) | yes           | no           | restriction   | allow |
| [shadow_unrelated](https://rust-lang.github.io/rust-clippy/master/#shadow_unrelated) | yes           | no           | restriction   | allow |

### 【描述】

在同一个作用域中，非必要时不要通过新变量声明遮蔽旧变量声明的方式来修改变量。

【正例】

```rust
let x = 2;
let y = x + 1; // 不改变x的值，声明新的变量y

let y = &x; // 不改变x的绑定，声明新的变量

let w = z; // 使用不同的名字
```

【反例】

```rust
let x = 2;
let x = x + 1; // 将会改变x的值

let x = &x; // 只是改变引用级别

let x = y; // 更早的绑定
let x = z; // 遮蔽了更早的绑定
```

## G.VAR.06  避免局部变量导致的大量栈分配

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| _ | no           | no           | _   | yes |

【定制化参考】
这条规则如果需要定制Lint，则可以分别检测每个局部变量占用的栈空间，并统计总体占用情况，进行告警。
局部变量占用栈空间的告警阈值默认是 2MB，但是用户可以按需配置该值。

### 【描述】

Rust 局部变量默认分配在栈上。当局部变量占用栈空间过大时，会栈溢出，可以采用Box<T>使变量在堆上分配

【正例】

```rust
let _: Box<[i32; 8000]> = Box::new([1; 8000]);
```

【反例】

```rust
let _: [i32; 8000] = [1; 8000];
```
