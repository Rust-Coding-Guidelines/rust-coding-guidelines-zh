
## G.STV.01  不宜直接使用可变静态变量作为全局变量

**【级别】** 建议

**【描述】**

对可变静态变量直接进行全局修改是 Unsafe 的。在多线程应用中，修改静态变量会导致数据争用（data race），此未定义行为目前并不会被 Clippy 或 Rustc 检测出。

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

若需要变更的值的类型为整数或布尔时，可直接使用 atomic。

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

若需修改整数或布尔之外的数据类型时，可考虑使用 Mutex 或 Rwlock 配合 once_cell 对全局变量进行变更。

（注: once_cell 目前已经被引入到 Nightly 版本的标准库中但还不稳定, 可参考 [std::lazy](https://doc.rust-lang.org/std/lazy/index.html)。若要在 Stable 版本下使用，则需要引入第三方库 [once_cell](https://docs.rs/once_cell/latest/once_cell/)。）

```rust
#![feature(once_cell)]          // 需要nightly compiler

use std::sync::Mutex;
use std::lazy::SyncLazy;        // 若使用stable版本则需要将之替换为once_cell::sync::Lazy

static GLOBAL_MESSAGE: SyncLazy<Mutex<String>> = SyncLazy::new(|| {
    Mutex::new(String::from("I'm hungry"))
});

fn update_msg(msg: &str) {
    let mut old_msg = GLOBAL_MESSAGE.lock().unwrap();
    *old_msg = msg.to_string();
}

fn main() {
    println!("{}", GLOBAL_MESSAGE.lock().unwrap()); // I'm hungry
    update_msg("I'm not hungry anymore!");
    println!("{}", GLOBAL_MESSAGE.lock().unwrap()); // I'm not hungry anymore!
}
```

上述示例亦可通过使用第三方库 [lazy_static](https://docs.rs/lazy_static/latest/lazy_static/) 的方式实现。

```rust
use std::sync::Mutex;
use lazy_static::lazy_static;

lazy_static! {
    static ref GLOBAL_MESSAGE: Mutex<String> = Mutex::new(String::from("I'm hungry"));
}

fn update_msg(msg: &str) {
    ...
}
...
```

**【例外】**

在使用FFI引用外部，例如C的函数时，其本身有可能会返回全局变量。当 rust 接入这些函数时需要指定输入的变量类型为静态（static），而若要改变它们的值的时候就需要将其定义为可变静态变量（static mut）。

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

通常情况下直接修改 static mut 会有线程安全风险，但若配合使用 [std::sync::Once](https://doc.rust-lang.org/std/sync/struct.Once.html#) 则可保证该变量只初始化一次，不会产生线程安全风险。

（注：此用法在功能上等同于 [once_cell::sync::OnceCell](https://docs.rs/once_cell/latest/once_cell/sync/struct.OnceCell.html) 或 Nightly 版本中的 [std::lazy::SyncOnceCell](https://doc.rust-lang.org/std/lazy/struct.SyncOnceCell.html)。但在使用 Stable 版本编译器并且不使用第三方库的条件下此写法完全合规，故算作例外情况。）

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

1. 代码中定义为 static mut 的变量是否仅被用于 FFI
2. 代码中定义为 static mut 的变量是否经过 call_once 初始化

