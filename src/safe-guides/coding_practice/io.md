# I/O

在标准库中也提供了标准 I/O 类型，在 Safe Rust 下，I/O 操作是足够安全的，但是对于 原生句柄 (Raw Fd) 的操作，则属于不安全。

在 Unsafe Rust 下也有相关 I/O  的规范，请参加 [Unsafe Rust - I/O](./unsafe_rust/io.md)   部分。

本部分只关注 Safe Rust 下 I/O 相关规范。

## 列表

- [P.IO.01 使用`read_to_end/read_to_string`方法时注意文件的大小能否一次性读入内存中](./io/P.IO.01.md)
- [G.IO.01 文件读取建议使用 `BufReader/BufWriter` 来代替 `Reader/Write`](./io/G.IO.01.md)