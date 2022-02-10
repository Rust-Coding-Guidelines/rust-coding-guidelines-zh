# Unsafe I/O

Rust 标准库提供了 I/O 安全性，保证程序持有私有的原始句柄（raw handle），其他部分无法访问它。但是 `FromRawFd::from_raw_fd` 是 Unsafe 的，所以在 Safe Rust中无法做到 `File::from_raw(7)` 这种事。 在这个文件描述符上面进行` I/O` 操作，而这个文件描述符可能被程序的其他部分私自持有。

## 列表

- [G.UNS.FIO.01 在使用原始句柄的时候，要注意 I/O 安全性](./io/G.UNS.FIO.01.md)