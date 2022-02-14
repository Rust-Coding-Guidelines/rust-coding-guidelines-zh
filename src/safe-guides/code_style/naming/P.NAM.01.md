## P.NAM.01  同一个crate中标识符的命名规则应该使用统一的词序

**【描述】**

当crate中类型名称都按照 **动词-宾语-error** 这样的顺序来命名错误类型时，如果要增加新的错误类型，则也需要按同样的词序来增加。
具体选择什么样的词序并不重要，但务必要保证同一个 crate 内词序的一致性。
若提供与标准库中相似功能的东西时，也要与标准库名称的词性顺序一致.


**【正例】**

以下是来自标准库的处理错误的一些类型：

- [`JoinPathsError`](https://doc.rust-lang.org/std/env/struct.JoinPathsError.html)
- [`ParseBoolError`](https://doc.rust-lang.org/std/str/struct.ParseBoolError.html)
- [`ParseCharError`](https://doc.rust-lang.org/std/char/struct.ParseCharError.html)
- [`ParseFloatError`](https://doc.rust-lang.org/std/num/struct.ParseFloatError.html)
- [`ParseIntError`](https://doc.rust-lang.org/std/num/struct.ParseIntError.html)
- [`RecvTimeoutError`](https://doc.rust-lang.org/std/sync/mpsc/enum.RecvTimeoutError.html)
- [`StripPrefixError`](https://doc.rust-lang.org/std/path/struct.StripPrefixError.html)

如果你想新增和标准库相似的错误类型，比如“解析地址错误”类型，为了保持词性一致，应该使用`ParseAddrError` 名称，而不是`AddrParseError`

```rust
struct ParseAddrError{}
```

**【反例】**

```rust
// 与标准库错误类型次序不一致，应该为 ParseAddrError
struct AddrParseError {}
```