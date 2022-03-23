## P.UNS.FIO.01  在使用原始句柄的时候，要注意 I/O 安全性

**【描述】**

很多 API 通过接受原始句柄来进行 I/O 操作：

```rust
pub fn do_some_io<FD: AsRawFd>(input: &FD) -> io::Result<()> {
    some_syscall(input.as_raw_fd())
}
```

`AsRawFd`并没有限制`as_raw_fd`的返回值，所以`do_some_io`最终可以在任意的`RawFd`值上进行 `I/O `操作。甚至可以写`do_some_io(&7)`，因为`RawFd`本身实现了`AsRawFd`。这可能会导致程序访问错误的资源。甚至通过创建在其他部分私有的句柄别名来打破封装边界，导致一些诡异的 远隔作用（Action at a distance）。

> **远隔作用**（**Action at a distance**）是一种程式设计中的[反模式](https://zh.wikipedia.org/wiki/反模式)，是指程式某一部分的行为会广泛的受到程式其他部分[指令](https://zh.wikipedia.org/wiki/指令)的影响，而且要找到影响其他程式的指令很困难，甚至根本无法进行。

在一些特殊的情况下，违反 I/O 安全甚至会导致内存安全。

