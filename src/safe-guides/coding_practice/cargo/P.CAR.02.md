## P.CAR.02 不要滥用 Features

**【描述】**

Rust 的 features 提供了方便的条件编译功能。从软件工程来说，features 适合应用于可选功能。

在使用 features 的时候，应该考虑到底是不是真的需要 features。

滥用 features 会带来额外的测试和静态检查的难度，需要保证不同 features 下的测试覆盖和静态检查情况。
