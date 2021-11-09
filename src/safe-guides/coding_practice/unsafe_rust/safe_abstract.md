# 安全抽象规范



使用 Unsafe Rust 的一种方式是将 Unsafe 的方法或函数进行安全抽象，将其变成安全的方法或函数。

Unsafe Rust 中 API 的安全性设计通常有两种方式：

1.  将内部的 unsafe API 直接暴露给 API 的使用者，并且使用 `unsafe` 关键字来声明该 API 是非安全的，同时也需要对安全边界条件添加注释。
2. 对 API 进行安全封装，即，安全抽象。在内部使用断言来保证当越过安全边界时可以 Panic，从而避免 UB 产生。

第二种方式，对 Unsafe 代码进行安全抽象，是 Rust 生态的一种约定俗成。

---

## P.UNS.SafeAbstract.01   代码中要注意是否会因为 Panic 发生而导致内存安全问题

**【描述】**

Panic 一般在程序达到不可恢复的状态才用，当然在 Rust 中也可以对一些实现了 `UnwindSafe` trait 的类型捕获恐慌。

当 Panic 发生时，会引发栈回退（stack unwind），调用栈分配对象的析构函数，并将控制流转移给恐慌处理程序中。所以，当恐慌发生的时候，当前存活变量的析构函数将会被调用，从而导致一些内存安全问题，比如释放已经释放过的内存。

通常， 封装的Unsafe 代码可能会暂时绕过所有权检查，而且，安全封装的 API 在内部unsafe 代码的值返回之前，会根据安全边界条件确保它不会违反安全规则。但是，假如封装的Unsafe 代码发生了恐慌，则其外部安全检查可能不会执行。这很可能导致类似 C/C++ 中 未初始化（Uninitialized ）或双重释放（Double Free）的内存不安全问题。

想要正确的推理在 Unsafe 代码中的恐慌安全，是非常困难且易于出错的。即便如此，在编写代码的时候也要刻意注意此类问题发生的可能性。

【示例】

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
        // 此处如果 f() 发生了恐慌，self 的长度就会不一致
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
 	unsafe { self.vec.set_len(len - del_bytes); } // + 修复bug 的代码 ，如果 while 里发生panic，则将返回长度设置为 0 
}

fn main(){
    // PoC: creates a non-utf-8 string in the unwinding path
    // 此处传入一个 非 UTF-8 编码字符串引发恐慌
    "0è0".to_string().retain(|_| {
        match the_number_of_invocation() {
            1 => false,
            2 => true,
            _ => panic!(),
        }
    });
}
```

## P.UNS.SafeAbstract.02    Unsafe 代码编写者有义务检查代码是否满足安全不变式

**【描述】**

安全不变式（见 [Unsafe 代码术语指南](./glossary.md) ）是 Rust 里的安全函数，在任何有效输入的情况下，都不应该发生任何未定义行为。

可以从以下三个方面来检查：

1. 逻辑一致性。
2. 纯洁性。相同的输入总是要返回相同的输出。
3. 语义约束。传入的参数要合法，满足数据类型。

【示例】

该代码是为 `Borrow<str>`实现 join 方法内部调用的一个函数 `join_generic_copy`的展示。 在 `join_generic_copy` 内部，会对 `slice` 进行两次转换，而在 `spezialize_for_lengths!` 宏内部，调用了`.borrow()`方法，如果第二次转换和第一次不一样，而会返回一个未初始化字节的字符串。

这里， `Borrow<B>` 是高阶类型，它内部 `borrow` 的一致性其实并没有保证，可能会返回不同的slice，如果不做处理，很可能会暴露出未初始化的字节给调用者。

```rust
// CVE-2020-36323: a higher-order invariant bug in join()
fn join_generic_copy<B, T, S>(slice: &[S], sep: &[T]) -> Vec<T> 
where T: Copy, B: AsRef<[T]> + ?Sized, S: Borrow<B>
{
    let mut iter = slice.iter();

    // `slice`is converted for the first time
    // during the buffer size calculation.
    let len = ...;  // `slice` 在这里第一次被转换	
    let mut result = Vec::with_capacity(len);
    // ...
    unsafe {
        let pos = result.len();
        let target = result.get_unchecked_mut(pos..len);
 
        // `slice`is converted for the second time in macro
        // while copying the rest of the components.
        spezialize_for_lengths!(sep, target, iter; // `slice` 第二次被转换
        0, 1, 2, 3, 4);
 
        // Indicate that the vector is initialized
        result.set_len(len);
    }
    result
}

// PoC: a benign join() can trigger a memory safety issue
impl Borrow<str> for InconsistentBorrow {
    fn borrow(&self) -> &str {
        if self.is_first_time() {
            "123456"
        } else {
            "0"
        }
    }
}

let arr: [InconsistentBorrow; 3] = Default::default();
arr.join("-");
```



## P.UNS.SafeAbstract.03    不要随便在公开的 API 中暴露未初始化内存

**【描述】**

在公开的API中暴露未初始化内存可能导致 UB。

【正例】

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



【反例】

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



## P.UNS.SafeAbstract.04   要考虑 Panic Safety 的情况

**【描述】**

要注意 Panic Safety 的情况，避免双重释放（double free）的问题发生。

在使用 `std::ptr` 模块中接口需要注意，容易产生 UB 问题，要多多查看 API 文档。

【正例】

 ```rust
 macro_rules! from_event_option_array_into_event_list(
     ($e:ty, $len:expr) => (
         impl<'e> From<[Option<$e>; $len]> for EventList {
             fn from(events: [Option<$e>; $len]) -> EventList {
                 let mut el = ManuallyDrop::new(
                     EventList::with_capacity(events.len())
                 );
 
                 for idx in 0..events.len() {
                     let event_opt = unsafe {
                         ptr::read(events.get_unchecked(idx))
                     };
 
                     if let Some(event) = event_opt {
                         // Use `ManuallyDrop` to guard against
                         // potential panic within `into()`.
                         // 当 into 方法发生 panic 当时候，这里 ManuallyDrop 可以保护其不会`double free`
                         let event = ManuallyDrop::into_inner(
                             ManuallyDrop::new(event)
                             .into()
                         );
                         el.push(event);
                     }
                 }
                 mem::forget(events);
                 ManuallyDrop::into_inner(el)
             }
         }
     )
 );
 ```

【反例】

```rust
//case 1
macro_rules! from_event_option_array_into_event_list(
    ($e:ty, $len:expr) => (
        impl<'e> From<[Option<$e>; $len]> for EventList {
                fn from(events: [Option<$e>; $len]) -> EventList {
                    let mut el = EventList::with_capacity(events.len());
                    for idx in 0..events.len() {
                    // 这个 unsafe 用法在 `event.into()`调用panic的时候会导致双重释放
                        let event_opt = unsafe { ptr::read(events.get_unchecked(idx)) };
                        if let Some(event) = event_opt { el.push::<Event>(event.into()); }
                    }
                    // 此处 mem::forget 就是为了防止 `dobule free`。
                    // 因为 `ptr::read` 也会制造一次 drop。
                    // 所以上面如果发生了panic，那就相当于注释了 `mem::forget`，导致`dobule free`
                    mem::forget(events);
                    el
                }
        }
    )
);
```



---



## G.UNS.SafeAbstract.01  在 公开的 unsafe 函数的文档中必须增加 `# Safety` 注释

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认 level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ---------- |
| [missing_safety_doc](https://rust-lang.github.io/rust-clippy/master/index.html#missing_safety_doc) | yes           | no           | Style      | warn       |

### 【描述】

在公开（pub）的 unsafe 函数文档中，必须增加 `# Safety` 注释来解释该函数的安全边界，这样使用该函数的用户才可以安全地使用它。

说明： 该规则通过 cargo clippy 来检测。默认会发出警告。

### 【示例】

【正例】

示例来自于标准库文档： [https://doc.rust-lang.org/stable/src/alloc/vec/mod.rs.html#1167](https://doc.rust-lang.org/stable/src/alloc/vec/mod.rs.html#1167)

```rust
    /// Creates a `Vec<T>` directly from the raw components of another vector.
    ///
    /// # Safety
    ///
    /// This is highly unsafe, due to the number of invariants that aren't
    /// checked:
    ///
    /// * `ptr` needs to have been previously allocated via [`String`]/`Vec<T>`
    ///   (at least, it's highly likely to be incorrect if it wasn't).
    /// * `T` needs to have the same size and alignment as what `ptr` was allocated with.
    ///   (`T` having a less strict alignment is not sufficient, the alignment really
    ///   needs to be equal to satisfy the [`dealloc`] requirement that memory must be
    ///   allocated and deallocated with the same layout.)
    /// * `length` needs to be less than or equal to `capacity`.
    /// * `capacity` needs to be the capacity that the pointer was allocated with.
    pub unsafe fn from_raw_parts(ptr: *mut T, length: usize, capacity: usize) -> Self {
        unsafe { Self::from_raw_parts_in(ptr, length, capacity, Global) }
    }
```


【反例】

```rust
    /// Creates a `Vec<T>` directly from the raw components of another vector.
    pub unsafe fn from_raw_parts(ptr: *mut T, length: usize, capacity: usize) -> Self {
        unsafe { Self::from_raw_parts_in(ptr, length, capacity, Global) }
    }
```

## G.UNS.SafeAbstract.02   在 Unafe 函数中应该使用 `assert!` 而非 `debug_assert!` 去校验边界条件

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认 level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ---------- |
| [debug_assert_with_mut_call](https://rust-lang.github.io/rust-clippy/master/index.html#debug_assert_with_mut_call) | yes           | no           | nursery    | allow      |

注意该 lint 当前是 Nursery Group，意味着可能会产生误报 Bug。

### 【描述】

`assert!` 宏 在 Release 和 Debug 模式下都会被检查，并且不能被禁用。它通常用来在  unsafe 函数中判断传入的参数是否满足某种边界条件，以此来防止不合法的参数传入导致未定义行为。

但是 `debug_assert!` 则可以通过配置 `-C debug-assertions` 来禁用它， 而且 `debug_assert!`    在 Release 模式下也会被编译器优化。所以，一旦使用了 `debug_assert!` 在 unsafe 函数中用来防范不合法参数，那有可能会失效。

【正例】

来自标准库 `slice` 的代码示例。

```rust
	pub fn split_at_mut(&mut self, mid: usize) -> (&mut [T], &mut [T]) {
        assert!(mid <= self.len()); // 判断边界条件，杜绝非法参数
        // SAFETY: `[ptr; mid]` and `[mid; len]` are inside `self`, which
        // fulfills the requirements of `from_raw_parts_mut`.
        unsafe { self.split_at_mut_unchecked(mid) }
    }
```

【反例】

```rust
	// 使用了 debug_assert! 那就说明这个校验在 Release 模式不一定有效
    // 那么该函数就要被标记为  unsafe
	pub unsafe fn split_at_mut(&mut self, mid: usize) -> (&mut [T], &mut [T]) {
        debug_assert!(mid <= self.len()); // 注意，这里是 debug_assert!
        // SAFETY: `[ptr; mid]` and `[mid; len]` are inside `self`, which
        // fulfills the requirements of `from_raw_parts_mut`.
        unsafe { self.split_at_mut_unchecked(mid) }
    }

   // or
   // 在 debug_assert_eq! 中包含可变引用的调用，
   // 也会因为 debug_assert_ 系列的断言宏在 Release 下产生不可预料的结果，它是 unsafe 的
   debug_assert_eq!(vec![3].pop(), Some(3));
```

## G.UNS.SafeAbstract.03    Unsafe 代码中手动实现 auto trait 需要注意

### 【级别：必须】

必须严格按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制参考】

Lint 需要检测 手工实现 auto trait 的行为，比如 `Sync/Send`，对开发者发出警告，要注意考虑其安全性

### 【描述】

所谓 auto trait 是指 Safe Rust中由编译器自动实现的 trait，比如 `Send/Sync` 。在 Unsafe Rust中就需要手动实现这俩 trait 了。

所以，在手动实现的时候要充分考虑其安全性。

【示例】

Rust futures 库中发现的问题，错误的手工 `Send/Sync`实现 破坏了线程安全保证。

受影响的版本中，`MappedMutexGuard`的`Send/Sync`实现只考虑了`T`上的差异，而`MappedMutexGuard`则取消了对`U`的引用。

当`MutexGuard::map()`中使用的闭包返回与`T`无关的`U`时，这可能导致安全Rust代码中的数据竞争。

这个问题通过修正`Send/Sync`的实现，以及在`MappedMutexGuard`类型中添加一个`PhantomData<&'a mut U>`标记来告诉编译器，这个防护也是在U之上。

```rust
// CVE-2020-35905: incorrect uses of Send/Sync on Rust's futures
pub struct MappedMutexGuard<'a, T: ?Sized, U: ?Sized> {
    mutex: &'a Mutex<T>,
    value: *mut U,
    _marker: PhantomData<&'a mut U>, // + 修复代码
}

impl<'a, T: ?Sized> MutexGuard<'a, T> {
    pub fn map<U: ?Sized, F>(this: Self, f: F)
        -> MappedMutexGuard<'a, T, U>
        where F: FnOnce(&mut T) -> &mut U {
            let mutex = this.mutex;
            let value = f(unsafe { &mut *this.mutex.value.get() });
                mem::forget(this);
                // MappedMutexGuard { mutex, value }
                MappedMutexGuard { mutex, value, _marker: PhantomData } //  + 修复代码
    }
}

// unsafe impl<T: ?Sized + Send, U: ?Sized> Send
unsafe impl<T: ?Sized + Send, U: ?Sized + Send> Send // + 修复代码
for MappedMutexGuard<'_, T, U> {}
//unsafe impl<T: ?Sized + Sync, U: ?Sized> Sync
unsafe impl<T: ?Sized + Sync, U: ?Sized + Sync> Sync // + 修复代码
for MappedMutexGuard<'_, T, U> {}

// PoC: this safe Rust code allows race on reference counter
* MutexGuard::map(guard, |_| Box::leak(Box::new(Rc::new(true))));
```



## G.UNS.SafeAbstract.04    不要随便在公开的 API 中暴露裸指针

### 【级别：必须】

必须严格按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制参考】

Lint需要检测在 pub 的结构体、枚举等类型中有裸指针字段或变体，对开发者发出警告，要注意考虑其安全性

### 【描述】

在公开的API中暴露裸指针，可能会被用户修改为空指针，从而有段错误风险。

【示例】

```rust
use cache;


/**

    `cache crate` 内部代码：

    ```rust
    pub enum Cached<'a, V: 'a> {
        /// Value could not be put on the cache, and is returned in a box
        /// as to be able to implement `StableDeref`
        Spilled(Box<V>),
        /// Value resides in cache and is read-locked.
        Cached {
            /// The readguard from a lock on the heap
            guard: RwLockReadGuard<'a, ()>,
            /// A pointer to a value on the heap
            // 漏洞风险
            ptr: *const ManuallyDrop<V>,
        },
        /// A value that was borrowed from outside the cache.
        Borrowed(&'a V),
    }
**/
fn main() {
    let c = cache::Cache::new(8, 4096);
    c.insert(1, String::from("test"));
    let mut e = c.get::<String>(&1).unwrap();

    match &mut e {
        cache::Cached::Cached { ptr, .. } => {
            // 将 ptr 设置为 空指针，导致段错误
            *ptr = std::ptr::null();
        },
        _ => panic!(),
    }
    // 输出：3851，段错误
    println!("Entry: {}", *e);
}
```



