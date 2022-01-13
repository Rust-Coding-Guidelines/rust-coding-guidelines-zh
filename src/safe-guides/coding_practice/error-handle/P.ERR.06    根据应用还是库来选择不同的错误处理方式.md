## P.ERR.06   根据应用还是库来选择不同的错误处理方式

**【描述】**

如果编写应用，建议使用` Error` trait对象；如果编写库，则建议返回自定义错误类型，方便下游处理


**【正例】**

```rust
// 对于应用使用 Error trait 对象更加方便
pub fn print(&self, languages: &Languages) -> Result<String, Box<dyn Error>> {
     // do something
}

// 对于库，暴露自定义错误类型更加方便下游处理错误
#[derive(Debug)]
pub struct SendError<T>(pub T);

impl<T> fmt::Display for SendError<T> {
    fn fmt(&self, fmt: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(fmt, "channel closed")
    }
}
