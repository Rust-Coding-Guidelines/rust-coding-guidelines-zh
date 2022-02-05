## P.UNS.SAS.03  不要随便在公开的 API 中暴露未初始化内存

**【描述】**

在公开的API中暴露未初始化内存可能导致 未定义行为。
关于未定义行为，可以参考[Unsafe 代码术语指南](./glossary.md)。

**【反例】**

```rust
// 以下是有安全风险的代码示例：
impl<R> BufRead for GreedyAccessReader<R>
    where
        R: Read,
{
    fn fill_buf(&mut self) -> IoResult<&[u8]> {
        if self.buf.capacity() == self.consumed {
            self.reserve_up_to(self.buf.capacity() + 16);
        }

        let b = self.buf.len();
        let buf = unsafe {
            // safe because it's within the buffer's limits
            // and we won't be reading uninitialized memory
            // 这里虽然没有读取未初始化内存，但是会导致用户读取
            std::slice::from_raw_parts_mut(
                self.buf.as_mut_ptr().offset(b as isize),
                self.buf.capacity() - b)
        };

        match self.inner.read(buf) {
            Ok(o) => {
                unsafe {
                    // reset the size to include the written portion,
                    // safe because the extra data is initialized
                    self.buf.set_len(b + o);
                }

                Ok(&self.buf[self.consumed..])
            }
            Err(e) => Err(e),
        }
    }

    fn consume(&mut self, amt: usize) {
        self.consumed += amt;
    }
}

// 另外一个漏洞代码
fn read_vec(&mut self) -> Result<Vec<u8>> {
    let len: u32 = de::Deserialize::deserialize(&mut *self)?;
    // 创建了未初始化buf
    let mut buf = Vec::with_capacity(len as usize);
    unsafe { buf.set_len(len as usize) }
    self.read_size(u64::from(len))?;
    // 将其传递给了用户提供的`Read`实现
    self.reader.read_exact(&mut buf[..])?;
    Ok(buf)
}
```

**【正例】**

 ```rust
 // 修正以后的代码示例，去掉了未初始化的buf：
 impl<R> BufRead for GreedyAccessReader<R>
     where
         R: Read,
 {
     fn fill_buf(&mut self) -> IoResult<&[u8]> {
         if self.buf.capacity() == self.consumed {
             self.reserve_up_to(self.buf.capacity() + 16);
         }
 
         let b = self.buf.len();
         self.buf.resize(self.buf.capacity(), 0);
         let buf = &mut self.buf[b..];
         let o = self.inner.read(buf)?;
 
         // truncate to exclude non-written portion
         self.buf.truncate(b + o);
 
         Ok(&self.buf[self.consumed..])
     }
 
     fn consume(&mut self, amt: usize) {
         self.consumed += amt;
     }
 }
 
 // 另外一个已修正漏洞的代码
 fn read_vec(&mut self) -> Result<Vec<u8>> {
     let len: u32 = de::Deserialize::deserialize(&mut *self)?;
     // 创建了未初始化buf
     let mut buf = Vec::with_capacity(len as usize);
     // 初始化为 0；
     buf.resize(len as usize, 0);
     self.read_size(u64::from(len))?;
     // 将其传递给了用户提供的`Read`实现
     self.reader.read_exact(&mut buf[..])?;
     Ok(buf)
 }
 ```
