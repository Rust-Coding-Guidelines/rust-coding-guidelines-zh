## P.ERR.01   当传入函数的参数值因为超出某种限制可能会导致函数调用失败，应该使用断言

**【描述】**

当传入函数的某个参数值可能因为超出某种限制，比如超出数组长度的索引、字符串是否包含某个字符、数组是否为空等，应该使用断言。

**【正例】**

```rust
// From: std::vec::Vec::swap_remove
#[stable(feature = "rust1", since = "1.0.0")]
pub fn swap_remove(&mut self, index: usize) -> T {
    #[cold]
    #[inline(never)]
    fn assert_failed(index: usize, len: usize) -> ! {
        panic!("swap_remove index (is {}) should be < len (is {})", index, len);
    }

    let len = self.len();
   
    if index >= len {
        // 此处使用断言方法，虽然不是标准库内置断言宏，但也是一种断言
        assert_failed(index, len);
    }
    unsafe {
        // We replace self[index] with the last element. Note that if the
        // bounds check above succeeds there must be a last element (which
        // can be self[index] itself).
        let last = ptr::read(self.as_ptr().add(len - 1));
        let hole = self.as_mut_ptr().add(index);
        self.set_len(len - 1);
        ptr::replace(hole, last)
    }
}
```