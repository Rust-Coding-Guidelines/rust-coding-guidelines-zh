# Unsafe I/O

Rust 标准库提供了 I/O 安全性，保证程序持有私有的原始句柄（raw handle），其他部分无法访问它。但是 `FromRawFd::from_raw_fd` 是 Unsafe 的，所以在 Safe Rust中无法做到 `File::from_raw(7)` 这种事。 在这个文件描述符上面进行` I/O` 操作，而这个文件描述符可能被程序的其他部分私自持有。

---

## G.UNS.IO.01  在使用原始句柄的时候，要注意 I/O 安全性

**【级别：必须】**

**【描述】**

很多 API 通过接受 原始句柄 来进行 I/O 操作：

```rust
pub fn do_some_io<FD: AsRawFd>(input: &FD) -> io::Result<()> {
    some_syscall(input.as_raw_fd())
}
```

`AsRawFd`并没有限制`as_raw_fd`的返回值，所以`do_some_io`最终可以在任意的`RawFd`值上进行 `I/O `操作。甚至可以写`do_some_io(&7)`，因为`RawFd`本身实现了`AsRawFd`。这可能会导致程序访问错误的资源。甚至通过创建在其他部分私有的句柄别名来打破封装边界，导致一些诡异的 远隔作用（Action at a distance）。

> **远隔作用**（**Action at a distance**）是一种程式设计中的[反模式](https://zh.wikipedia.org/wiki/反模式)，是指程式某一部分的行为会广泛的受到程式其他部分[指令](https://zh.wikipedia.org/wiki/指令)的影响，而且要找到影响其他程式的指令很困难，甚至根本无法进行。

在一些特殊的情况下，违反 I/O 安全甚至会导致内存安全。

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制参考】

检测在 IO 时使用  `as_raw_fd` 调用时，警告开发者这是 Unsafe 的，要对传入的原始文件描述符的安全性进行考察。

