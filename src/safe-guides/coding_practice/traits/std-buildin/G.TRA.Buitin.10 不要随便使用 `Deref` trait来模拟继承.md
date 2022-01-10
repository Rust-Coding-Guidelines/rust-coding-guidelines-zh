## P.TRA.Buitin.01 不要随便使用`Deref`trait来模拟继承

**【级别】** 建议

**【描述】**

`Deref` trait是专门用于实现自定义指针类型而存在的。虽然可以实现 `Deref` 来达到某种类似于继承的行为，但 Rust 中不推荐这样做。

这是因为 Rust 语言推崇显式的转换，而 `Deref` 则是 Rust 中为数不多的隐式行为。如果 `Deref` 被滥用，那么程序中隐式行为可能会增多，隐式的转换是 Bug 的温床。

**【反例】**
不要像下面这样用`Deref`来模拟继承。
```rust
use std::ops::Deref;

struct Foo {}

impl Foo {
    fn m(&self) {
        // ...
    }
}

struct Bar {
    f: Foo
}

impl Deref for Bar {
    type Target = Foo;

    fn deref(&self) -> &Foo {
        &self.f
    }
}

fn main() {
    let bar = Bar { f: Foo {} };
    bar.m();
}

```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| - | no           | no           | -   | yes |
