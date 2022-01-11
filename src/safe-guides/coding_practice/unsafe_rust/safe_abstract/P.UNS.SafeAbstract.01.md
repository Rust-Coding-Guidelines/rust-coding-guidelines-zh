## P.UNS.SafeAbstract.01  代码中要注意是否会因为 Panic 发生而导致内存安全问题

**【描述】**

Panic 一般在程序达到不可恢复的状态才用，当然在 Rust 中也可以对一些实现了`UnwindSafe` trait 的类型捕获Panic。

当 Panic 发生时，会引发栈回退（stack unwind），调用栈分配对象的析构函数，并将控制流转移给 Panic 处理程序中。所以，当 Panic 发生的时候，当前存活变量的析构函数将会被调用，从而导致一些内存安全问题，比如释放已经释放过的内存。

通常， 封装的 Unsafe 代码可能会暂时绕过所有权检查，而且，安全封装的 API 在内部 unsafe 代码的值返回之前，会根据安全边界条件确保它不会违反安全规则。但是，假如封装的 Unsafe 代码发生了Panic，则其外部安全检查可能不会执行。这很可能导致类似 C/C++ 中 未初始化（Uninitialized ）或双重释放（Double Free）的内存不安全问题。

想要正确的推理在 Unsafe 代码中的恐慌安全，是非常困难且易于出错的。即便如此，在编写代码的时候也要刻意注意此类问题发生的可能性。

**【正例】**

```rust
// 标准库 `String::retain()` 曝出的 CVE-2020-36317 Panic safety bug

pub fn retain<F>(&mut self, mut f: F)
where 
    F: FnMut(char) -> bool
{
    let len = self.len();
    let mut del_bytes = 0;
 	let mut idx = 0;
 
    unsafe { self.vec.set_len(0); }    // + 修复bug 的代码
 	while idx < len {
 		let ch = unsafe {
  			self.get_unchecked(idx..len).chars().next().unwrap()
 		};
 		let ch_len = ch.len_utf8();
 
 		// self is left in an inconsistent state if f() panics
        // 此处如果 f() 发生了 Panic，self 的长度就会不一致
 		if !f(ch) {
 			del_bytes += ch_len;
 		} else if del_bytes > 0 {
 			unsafe {
 				ptr::copy(self.vec.as_ptr().add(idx),
 				self.vec.as_mut_ptr().add(idx - del_bytes),
 				ch_len);
 			}
 		}
 		idx += ch_len; // point idx to the next char
 	}
 	unsafe { self.vec.set_len(len - del_bytes); } // + 修复bug 的代码 ，如果 while 里发生 Panic，则将返回长度设置为 0 
}

fn main(){
    // PoC: creates a non-utf-8 string in the unwinding path
    // 此处传入一个 非 UTF-8 编码字符串引发 Panic
    "0è0".to_string().retain(|_| {
        match the_number_of_invocation() {
            1 => false,
            2 => true,
            _ => panic!(),
        }
    });
}
```
