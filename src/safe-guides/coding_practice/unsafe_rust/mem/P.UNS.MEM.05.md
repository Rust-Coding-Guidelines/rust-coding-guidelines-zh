## P.UNS.MEM.05  如果需要使用位域，推荐使用第三方库

**【描述】**

位域（或称“位段”， Bit field）为一种数据结构，可以把数据以位的形式紧凑的存储，并允许程序员对此结构的位进行寻址和操作。

这种数据结构的好处：

- 可以使数据单元节省存储空间，当程序需要成千上万个数据单元时，这种方法就显得尤为重要。
- 位域可以很方便的访问一个整数值的部分内容从而可以简化程序源代码。

而位域这种数据结构的缺点在于，其内存分配与内存对齐的实现方式依赖于具体的机器和系统，在不同的平台可能有不同的结果，这导致了位域在本质上是不可移植的。所以目前 Rust 并未在语言内置位域支持。

Rust 中使用位域时推荐使用的第三方库：

- [bitvec](https://github.com/bitvecto-rs/bitvec)，是 Cpp 中 `std::vector<bool>`和`std::bitset` 数据结构的 Rust 实现。在性能、功能性、API完整性和表现力方面，该库都非常优秀。
- [bitflags](https://github.com/bitflags/bitflags)，提供了方便的宏语法来定义和处理位域。
- [modular-bitfield](https://github.com/Robbepop/modular-bitfield)，提供了一些宏来定义和处理位域，并且是完全基于 Safe Rust。

以上三个库都支持 `no-std`。

**【正例】**

以 `bitvec` 为例。 示例参考：[https://myrrlyn.net/blog/misc/bitfields-in-rust](https://myrrlyn.net/blog/misc/bitfields-in-rust)

C 语言中定义位域。

```C
Struct SixFlags {
  uint16_t eins : 3;
  uint16_t zwei : 2;
  uint16_t drei : 3;
  uint16_t vier : 3;
  uint16_t funf : 2;
  uint16_t seid : 3;
}
```

```rust
type SixFlagsBits = BitSlice<Local, u16>;

#[repr(C)]
#[derive(Copy, Clone, Default)]
pub struct SixFlags {
  inner: u16,
};

impl SixFlags {
  pub fn eins(&self) -> &SixFlagsBits {
    &self.inner.bits()[0 .. 3]
  }

  pub fn eins_mut(&mut self) -> &mut SixFlagsBits {
    &mut self.inner.bits()[0 .. 3]
  }

  pub fn zwei(&self) -> &SixFlagsBits {
    &self.inner.bits()[3 .. 5]
  }

  pub fn zwei_mut(&mut self) -> &mut SixFlagsBits {
    &mut self.inner.bits()[3 .. 5]
  }
}

fn main() {
    let mut flags = SixFlags::default();
    flags.eins_mut().store(2u8);
    flags.zwei_mut().store(0u8);
    flags.drei_mut().store(4u8);
    flags.vier_mut().store(5u8);
    flags.funf_mut().store(1u8);
    flags.seis_mut().store(7u8); 
}






```