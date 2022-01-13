## P.CAR.02 不要滥用 `features` 

**【描述】**

Rust 的 features ，提供了方便的条件编译功能。从软件工程来说，features 应该是为了避免让用户依赖没必要依赖的功能而使用的。

在使用 features 的时候，应该考虑到底是不是真的需要 features。

滥用features会带来额外的测试和静态检查的难度，需要保证不同features下的测试覆盖和静态检查情况。
