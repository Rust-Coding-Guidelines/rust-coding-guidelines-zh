# I/O

在标准库中也提供了标准 I/O 类型，在 Safe Rust 下，I/O 操作是足够安全的，但是对于 原生句柄 (Raw Fd) 的操作，则属于不安全。

在 Unsafe Rust 下也有相关 I/O  的规范，请参加 [Unsafe Rust - I/O](./unsafe_rust/io.md)   部分。

本部分只关注 Safe Rust 下 I/O 相关规范。

---
<!-- toc -->
---

## P.IO.01   文件读取建议使用 `BufReader/BufWriter` 来代替 `Reader/Write`

**【描述】**

`BufReader/BufWriter` 使用缓冲区来减少 I/O 请求的次数，提升性能。访问磁盘一次读取 256 个字节显然比 访问磁盘256次每次一个字节 效率要更高。

【示例】

```rust
use std::fs::File;
use std::io::{BufReader, Read};

fn main() {
    let mut data = String::new();
    let f = File::open("/etc/hosts").expect("Unable to open file");
    let mut br = BufReader::new(f);
    br.read_to_string(&mut data).expect("Unable to read string");
    println!("{}", data);
}
```

写 I/O：

```rust
use std::fs::File;
use std::io::{BufWriter, Write};

fn main() {
    let data = "Some data!";
    let f = File::create("/tmp/foo").expect("Unable to create file");
    let mut f = BufWriter::new(f);
    f.write_all(data.as_bytes()).expect("Unable to write data");
}
```

 逐行读： 注意返回的每行字符串都不含有换行字符。

```rust
use std::fs::File;
use std::io::{BufRead, BufReader};

pub fn scan() -> Result<(), io::Error> {
    let mut file = BufReader::new(try!(File::open("foo.txt")));

    let mut line = String::new();
    while try!(file.read_line(&mut line)) != 0 {
        if line.starts_with("x") {
            try!(file.seek(SeekFrom::Start(1000)));
        }
        do_stuff(&line);
        line.clear();
    }

    Ok(())
}
```

## P.IO.02    使用 `read_to_end/read_to_string`方法时注意文件的大小能否一次性读入内存中

**【描述】**

对于内存可以一次性读完的文件，可以使用 `read_to_end/read_to_string`之类的方法。但是如果你想读任意大小的文件，则不适合使用它们。



