## P.MEM.Lifetime.02 通常需要显式地标注生命周期，而非利用编译器推断

**【描述】**

编译器对生命周期参数有两种单态化方式（生命周期参数也是一种泛型）：

- Early bound。一般情况下，`'a: 'b` 以及 `impl<'a>` 这种方式是 early bound，意味着这些生命周期参数会在当前作用域单态化生命周期实例。
- Late bound。默认的 `'a` 或 `for<'a>` 是在实际调用它们的地方才单态化生命周期实例。

在不同的场景下，需要指定合适的单态化方式，才能让编译器明白开发者的意图。

在使用匿名生命周期 `'_` 的时候需要注意，如果有多个匿名生命周期，比如 `('_，'_)` ，每个匿名生命周期都会有自己的单独实例。

**【反例】**

```rust
fn main() {
    let v = vec![1, 2, 3, 4, 5, 6];
    let mut buf = Buffer::new(&v);
    // error[E0499]: cannot borrow `buf` as mutable more than once at a time
    let b1 = buf.read_bytes();
    let b2 = buf.read_bytes();
    println!("{:#?} {:#?}", b1, b2);
}

struct Buffer<'a> {
    buf: &'a [u8],
    pos: usize,
}

impl<'a> Buffer<'a> {
    fn new(b: &'_ [u8]) -> Buffer {
        Buffer { buf: b, pos: 0 }
    }
    // 此处依赖编译器推断的生命周期将导致main函数中该方法调用编译错误
    fn read_bytes(&'_ mut self) -> &'_ [u8] {
        self.pos += 3;
        &self.buf[self.pos - 3..self.pos]
    }
}
```

**【正例】**

```rust
fn main() {
    let v = vec![1, 2, 3, 4, 5, 6];
    let mut buf = Buffer::new(&v);
    let b1 = buf.read_bytes();
    let b2 = buf.read_bytes();
    println!("{:#?} {:#?}", b1, b2);
}

struct Buffer<'a> {
    buf: &'a [u8],
    pos: usize,
}

// 明确标示清楚生命周期，向编译器传达开发者意图，则可正常编译
impl<'b, 'a: 'b> Buffer<'a> {
    fn new(b: &'_ [u8]) -> Buffer {
        Buffer { buf: b, pos: 0 }
    }
    // 明确标示清楚输入引用和输出引用的生命周期关系是 `'a: 'b`
    fn read_bytes(&'b mut self) -> &'a [u8] {
        self.pos += 3;
        &self.buf[self.pos - 3..self.pos]
    }
}
```