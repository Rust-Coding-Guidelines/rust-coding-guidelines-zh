# 数组

这里指固定长度数组。注意，不同长度的数组，类型是不一样的。比如 `[T;1]`和 `[T;3]` 就是不同类型。

从  Rust 1.51 版本开始，稳定了 常量泛型（const generics）功能，形如  `[T;1]`和 `[T;3]` 这种不同的类型可以统一为 `[T; N]`。



---
<!-- toc -->
---

## P.TYP.Array.01 当数组长度在编译期就已经确定，应优先使用固定长度数组，而非动态数组（ `Vec<T>`）

**【描述】**

固定长度数组会根据元素类型，优先选择存储在栈上，可以优化内存分配。

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

## G.TYP.Array.01 创建大全局数组时宜使用静态变量而非常量

**【级别】** 建议

**【描述】**

因为常量会内联，对于大的数组，使用静态变量定义更好。

相关：[G.CNS.03 ](./consts.md)

**【反例】**

```rust
pub const A: [u32;1_000_000] = [0u32; 1_000_000];
```

**【正例】**

```rust
pub static A: [u32;1_000_000] = [0u32; 1_000_000];
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [large_const_arrays](https://rust-lang.github.io/rust-clippy/master/#large_const_arrays) | yes           | no           | perf       | warn  |
| [large_stack_arrays](https://rust-lang.github.io/rust-clippy/master/#large_stack_arrays) | yes           | no           | pedantic   | allow |

注意： `large_stack_arrays` 会检查在栈上分配的大数组，但clippy默认是 allow，根据实际使用场景决定是否针对这种情况发出警告。



## G.TYP.Array.02  使用数组索引时禁止越界访问

**【级别】** 要求

**【描述】**

越界访问在运行时会 Panic！

**【反例】**

```rust
let x = [1, 2, 3, 4];
x[9];
&x[2..9];
```

**【正例】**

```rust
let x = [1, 2, 3, 4];
x[0];
x[3];
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group      | level |
| ------------------------------------------------------------ | ------------- | ------------ | --------------- | ----- |
| [out_of_bounds_indexing](https://rust-lang.github.io/rust-clippy/master/#out_of_bounds_indexing) | yes           | no           | **correctness** | deny  |


## G.TYP.Array.03  当数组元素为原生数据类型（Primitive），排序时宜使用非稳定排序

**【级别】** 建议

**【描述】**

稳定排序会消耗更多的内存和 CPU 周期，相对而言，非稳定排序性能更佳。

当然，在必须要稳定排序的场合，不应该使用非稳定排序。

**【反例】**

```rust
let mut vec = vec![2, 1, 3];
vec.sort();  // stable sort
```

**【正例】**

```rust
let mut vec = vec![2, 1, 3];
vec.sort_unstable(); // unstable sort
```

**【例外】**

```rust
// https://docs.rs/crate/solana-runtime/1.7.11/source/src/accounts_db.rs#:~:text=clippy%3a%3astable_sort_primitive
 pub fn generate_index(&self, limit_load_slot_count_from_snapshot: Option<usize>) {
        let mut slots = self.storage.all_slots();
        #[allow(clippy::stable_sort_primitive)]
        slots.sort(); // The business requirement here is to use stable sort 
        // ...
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [stable_sort_primitive](https://rust-lang.github.io/rust-clippy/master/#stable_sort_primitive) | yes           | no           | **perf**   | warn  |

当确实需要稳定排序时，需要修改该 lint 的设置为 `allow`。

