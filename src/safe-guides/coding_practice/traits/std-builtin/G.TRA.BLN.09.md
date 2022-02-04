## G.TRA.BLN.09   一般情况下不要给 Copy 类型手工实现 Clone 

**【级别】** 建议

**【描述】**

手工为 Copy 类型实现 Clone ，并不能改变 Copy 类型的行为。除非你显式地去调用 `clone()`方法。

**【反例】**

```rust
#[derive(Copy)]
struct Foo;

impl Clone for Foo {
    // ..
}
```

**【正例】**

```rust
#[derive(Copy, Clone)]
struct Foo;
```

**【例外】**

在有些情况下，需要手动实现 Copy 和 Clone 。 相关 issues : [https://github.com/rust-lang/rust/issues/26925](https://github.com/rust-lang/rust/issues/26925) 

```rust
use std::marker::PhantomData;

struct Marker<A>(PhantomData<A>);

// 如果使用 Derive 自动实现的话，会要求 Marker<A> 里的 A 也必须实现 Clone
// 这里通过手工给 Marker<A> 实现 Copy 和 Clone 可以避免这种限制
impl<A> Copy for Marker<A> {}
impl<A> Clone for Marker<A> {
    fn clone(&self) -> Self {
        *self
    }
}

// 不需要给 NoClone 实现 Clone
struct NoClone;
fn main() {
    let m: Marker<NoClone> = Marker(PhantomData);
    let m2 = m.clone();
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [expl_impl_clone_on_copy](https://rust-lang.github.io/rust-clippy/master/#expl_impl_clone_on_copy) | yes           | no           | pedantic   | allow |

