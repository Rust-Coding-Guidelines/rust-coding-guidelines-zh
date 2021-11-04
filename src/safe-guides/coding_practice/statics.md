# 静态变量


---

## P.STV.01  不要直接使用可变静态变量作为全局变量

### 【描述】

对可变静态变量进行全局修改是 Unsafe 的。在多线程应用中，修改静态变量会导致数据争用（data race），此未定义行为目前并不会被Clippy或Rustc检测出。

### 

【例外】

在使用FFI引用外部，例如C的函数时，其本身有可能会返回全局变量。当rust接入这些函数时需要指定输入的变量类型为静态（static），而若要改变它们的值的时候就需要将其定义为可变静态变量（static mut）。

```rust
use std::ffi::CString;
use std::ptr;

#[link(name = "readline")]
extern {
    static mut rl_prompt: *const libc::c_char;
}

fn main() {
    let prompt = CString::new("[my-awesome-shell] $").unwrap();
    unsafe {
        rl_prompt = prompt.as_ptr();

        println!("{:?}", rl_prompt);

        rl_prompt = ptr::null();
    }
}
```


