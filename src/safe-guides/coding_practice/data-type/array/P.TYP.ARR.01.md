## P.TYP.ARR.01 当数组长度在编译期就已经确定，应优先使用固定长度数组，而非动态数组（ `Vec<T>`）

**【描述】**

固定长度数组会根据元素类型，优先选择存储在栈上，可以优化内存分配。当然，过大的数组可以酌情考虑放到堆内存，这个依据具体场景来决定。

当编译期长度可以确定，但长度并不是唯一确定的，那么可以考虑使用常量泛型。注意：常量泛型特性从 Rust 1.51版稳定。

**【示例】**

```rust
pub struct Grid {
    array: [u32; 5],
    width: usize,
    height: usize,
}
```

常量泛型：

```rust
pub struct Grid<T, const W: usize, const H: usize>
where
{
    array: [[T; W]; H],
}

impl<T, const W: usize, const H: usize> Default for Grid<T, W, H>
where
    T: Default + Copy,
{
    fn default() -> Self {
        Self {
            array: [[T::default(); W ]; H],
        }
    }
}

const WIDTH: usize = 300;
const HEIGHT: usize = 200;

fn main(){
    let _g = Grid::<usize, 3, 4>::default();  
    let _h = Grid::<usize, WIDTH, HEIGHT>::default();
}
```

注意，常量泛型目前还有一些特性并未完善，比如下面示例中的 `#![feature(generic_const_exprs)]` 特性，需要在 Nightly Rust 下使用。

```rust
#![feature(generic_const_exprs)]
pub struct Grid<T, const W: usize, const H: usize>
where
    [(); W * H]: Sized,
{
    array: [T; W * H],
}

impl<T, const W: usize, const H: usize> Default for Grid<T, W, H>
where
    [(); W * H]: Sized,
    T: Default + Copy,
{
    fn default() -> Self {
        Self {
            array: [T::default(); W * H],
        }
    }
}

const WIDTH: usize = 300;
const HEIGHT: usize = 200;

fn main(){
    let _g = Grid::<usize, 3, 4>::default();  
    let _h = Grid::<usize, WIDTH, HEIGHT>::default();
}

```

---

