# 静态变量

---
<!-- toc -->
---

## G.STV.01  不宜直接使用可变静态变量作为全局变量

**【级别】** 建议

**【描述】**

对可变静态变量直接进行全局修改是 Unsafe 的。在多线程应用中，修改静态变量会导致数据争用（data race），此未定义行为目前并不会被Clippy或Rustc检测出。

**【反例】**

```rust
static mut NUM_OF_APPLES: usize = 0;

unsafe fn buy_apples(count: usize) {
    NUM_OF_APPLES += count;
}

unsafe fn eat_apple() {
    NUM_OF_APPLES -= 1;
}
```

**【正例】**

若需要变更的值的类型为整数或布尔时，可直接使用atomic。

```rust
use std::sync::atomic::{AtomicUsize, Ordering::SeqCst};

static NUM_OF_APPLES: AtomicUsize = AtomicUsize::new(0);

fn buy_apple(count: usize) {
    NUM_OF_APPLES.fetch_add(count, SeqCst);
}

fn eat_apple() {
    NUM_OF_APPLES.fetch_sub(1, SeqCst);
}
```

**【正例】**

若需修改整数或布尔之外的数据类型时，可考虑使用Mutex或Rwlock配合lazy_static宏的方式对全局变量进行变更。

```rust
use std::sync::Mutex;
use lazy_static::lazy_static;

lazy_static! {
    static ref MESSAGE: Mutex<String> = Mutex::new(String::from("I'm hungry"));
}

fn update_msg() {
    let mut old_msg = MESSAGE.lock().unwrap();
    *old_msg = String::from("I'm not hungry anymore!");
}

fn main() {
    println!("{}", MESSAGE.lock().unwrap()); // I'm hungry
    update_msg();
    println!("{}", MESSAGE.lock().unwrap()); // I'm not hungry anymore!
}
```

**【例外】**

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

**【例外】**

通常情况下直接修改static mut会有线程安全风险，但若配合使用[std::sync::Once](https://doc.rust-lang.org/std/sync/struct.Once.html#)则可保证该变量只初始化一次，不会产生线程安全风险。

```rust
use std::sync::{Mutex, Once};

static mut SOUND: Option<Mutex<String>> = None;
static SOUND_ONCE: Once = Once::new();

fn make_sound() -> &'static Mutex<String> {
    unsafe {
        SOUND_ONCE.call_once(|| {
            SOUND = Some(Mutex::new("Oh! Apple! nom nom nom...".to_string()));
        });
        SOUND.as_ref().unwrap()
    }
}

fn main() {
    println!("{}", *make_sound().lock().unwrap()); // Oh! Apple! nom nom nom...
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| _ | no           | no           | _   | yes |

**【定制化参考】**

这条规则如果需要定制 Lint，则应考虑两种情况:

1. 定义为static mut的变量是否被用于FFI
2. 定义为static mut的变量在仅被用在call_once或call_once_force等方法的闭包内

