## G.FIO.01  文件读取建议使用 `BufReader/BufWriter` 来代替 `Reader/Write`

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
