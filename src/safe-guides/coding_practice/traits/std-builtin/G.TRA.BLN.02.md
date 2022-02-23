## G.TRA.BLN.02   不要为迭代器实现`Copy` 特质

**【级别】** 要求

**【描述】**

不要为迭代器实现 `Copy` 特质，因为一般会存在有改变状态的迭代器，如果实现 `Copy`，则可能会被意外隐式复制，违反 Rust 编译器可变借用独占原则，可能会导致一些意外行为。

所以，通常可以只为迭代器实现 `Clone`，需要复制的时候显式拷贝，如果出现问题也容易排查。

**【反例】**

```rust
use std::marker::PhantomData;

#[derive(Debug)]
struct ABC {
    a: [i32; 3],
    b: [i32; 3],
    c: [i32; 3],
}

impl ABC {
    fn iter_mut(&mut self) -> ABCIterMut {
        ABCIterMut {
            abc: self,
            index: 0,
            _phantom: PhantomData::default(),
        }
    }

}

// 这里为迭代器 ABCIterMut<'a>  实现 Copy 
#[derive(Copy, Clone)]
struct ABCIterMut<'a> {
    abc: *mut ABC,
    index: u8,
    _phantom: PhantomData<&'a mut ABC>,
}

impl<'a> Iterator for ABCIterMut<'a> {
    type Item = &'a mut [i32; 3];
    fn next(&mut self) -> Option<Self::Item> {

        let value = unsafe {
            match self.index {
                0 => &mut (*self.abc).a,
                1 => &mut (*self.abc).b,
                2 => &mut (*self.abc).c,
                _ => return None
            }
        };
        self.index += 1;
        Some(value)
    }
}

fn main(){
    let mut abc = ABC{a: [1; 3], b: [2;3], c: [3;3]};
    let mut abc_iter_mut = abc.iter_mut();


    // 因为实现 Copy，迭代器这里会隐式复制，返回结构体内部字段可变借用
    for i in abc_iter_mut {
        // Do Something
    }

    // 因为实现 Copy，迭代器这里会隐式复制，返回结构体内部字段可变借用
    // 存在意外风险而不容易被发现
    for i in abc_iter_mut {
        // Do Something
    }

}
```

另外，对于标准库里的 `Range<T>` 就不能实现 Copy，因为它也是一个迭代器。

细节可以参考来自官方讨论： [https://github.com/rust-lang/rust/pull/27186#issuecomment-123390413](https://github.com/rust-lang/rust/pull/27186#issuecomment-123390413)


**【正例】**


```rust
use std::marker::PhantomData;

#[derive(Debug)]
struct ABC {
    a: [i32; 3],
    b: [i32; 3],
    c: [i32; 3],
}

impl ABC {
    fn iter_mut(&mut self) -> ABCIterMut {
        ABCIterMut {
            abc: self,
            index: 0,
            _phantom: PhantomData::default(),
        }
    }

}

// 不实现 Copy
// 在需要的时候只实现 Clone
#[derive(Clone)]
struct ABCIterMut<'a> {
    abc: *mut ABC,
    index: u8,
    _phantom: PhantomData<&'a mut ABC>,
}

impl<'a> Iterator for ABCIterMut<'a> {
    type Item = &'a mut [i32; 3];
    fn next(&mut self) -> Option<Self::Item> {

        let value = unsafe {
            match self.index {
                0 => &mut (*self.abc).a,
                1 => &mut (*self.abc).b,
                2 => &mut (*self.abc).c,
                _ => return None
            }
        };
        self.index += 1;
        Some(value)
    }
}

fn main(){
    let mut abc = ABC{a: [1; 3], b: [2;3], c: [3;3]};
    let mut abc_iter_mut = abc.iter_mut();

    // 因为只实现了 Clone
    // 需要用多次迭代器的时候，显式调用 clone() 方法，
    // 开发者可以明确地知道自己在做什么
    for i in abc_iter_mut.clone() {
        println!("{i:?}");
    }

    for i in abc_iter_mut {
        println!("{i:?}")
    }

}

```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group   | level |
| ------------------------------------------------------------ | ------------- | ------------ | ------------ | ----- |
| [copy_iterator](https://rust-lang.github.io/rust-clippy/master/#copy_iterator) | yes           | no           | **pedantic** | allow |