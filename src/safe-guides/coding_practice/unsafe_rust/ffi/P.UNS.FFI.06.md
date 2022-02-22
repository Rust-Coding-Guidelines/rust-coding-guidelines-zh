## P.UNS.FFI.06 Rust 和 C 之间传递字符或字符串时需要注意字符串要符合 C-ABI 以及 字符串的编码

**【描述】**

注意要使用 `c_char` 对应 C 语言的字符。`libc::c_char` 和 `std::os::raw::c_char` 在大多数 64位 linux 上都是相同的。

FFi 接口使用的字符串要符合 C 语言约定，即使用 `\0` 结尾且中间不要包含 `\0`字符的字符串。

Rust 中字符串要求 `utf-8` 编码，而 C 字符串则没有这个要求。所以需要注意编码。

**【反例】**

```rust
let f = libc::fopen("/proc/uptime".as_ptr().cast(), "r".as_ptr().cast());
// 即使 /proc/uptime 文件存在，fopen 系统调用也会返回 NULL
// 并且将错误码 errno 标记为 2 ("No such file or directory")
```

**【正例】**

```rust
let f = libc::fopen("/proc/uptime\0".as_ptr().cast(), "r\0".as_ptr().cast());
```


