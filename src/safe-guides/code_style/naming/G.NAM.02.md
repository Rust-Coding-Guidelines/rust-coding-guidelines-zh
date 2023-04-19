## G.NAM.02 类型转换函数命名需要遵循所有权语义

**【级别】** 建议

**【描述】**

进行特定类型转换的方法名应该包含以下前缀：

| 名称前缀 | 内存代价 | 所有权 |
| ------ | ---- | --------- |
| `as_` | 无代价 | borrowed -\> borrowed |
| `to_` | 代价昂贵 | borrowed -\> borrowed<br>borrowed -\> owned (非 Copy 类型)<br>owned -\> owned (Copy 类型) |
| `into_` | 看情况 | owned -\> owned (非 Copy 类型) |

以 `as_` 和 `into_` 作为前缀的类型转换通常是 *降低抽象层次* ，要么是查看背后的数据 ( `as` ) ，要么是分解 (deconstructe) 背后的数据 ( `into` ) 。
相对来说，以 `to_` 作为前缀的类型转换处于同一个抽象层次，但是底层会做更多工作，比如多了内存拷贝等操作。

当一个类型用更高级别的语义 (higher-level semantics) 封装 (wraps) 一个内部类型时，应该使用 `into_inner()` 方法名来取出被封装类型的值。

这适用于以下封装器：

读取缓存 ([`BufReader`](https://doc.rust-lang.org/std/io/struct.BufReader.html#method.into_inner)) 、编码或解码 ([`GzDecoder`](https://docs.rs/flate2/1.0.22/flate2/read/struct.GzDecoder.html#method.into_inner)) 、取出原子 ([`AtomicBool`](https://doc.rust-lang.org/std/sync/atomic/struct.AtomicBool.html#method.into_inner) 、
或者任何相似的语义封装 ([`BufWriter`](https://doc.rust-lang.org/stable/std/io/struct.BufWriter.html#method.into_inner))。


**【正例】**

标准库 API 命名有如下示例：

- `as_`
    - [`str::as_bytes()`](https://doc.rust-lang.org/std/primitive.str.html#method.as_bytes) 
      用于查看 UTF-8 字节的 `str` 切片，这是无内存代价的（不会产生内存分配）。 传入值是 `&str` 类型，输出值是 `&[u8]` 类型。
- `to_`
    - [`Path::to_str`](https://doc.rust-lang.org/stable/std/path/struct.Path.html#method.to_str)
      对操作系统路径进行 UTF-8 字节检查，开销昂贵。
      虽然输入和输出都是借用，但是这个方法对运行时产生不容忽视的代价，
      所以不应使用 `as_str` 名称。
    - [`str::to_lowercase()`](https://doc.rust-lang.org/std/primitive.str.html#method.to_lowercase)
      生成正确的 Unicode 小写字符，
      涉及遍历字符串的字符，可能需要分配内存。
      输入值是 `&str` 类型，输出值是 `String` 类型。
    - [`f64::to_radians()`](https://doc.rust-lang.org/std/primitive.f64.html#method.to_radians)
      把浮点数的角度制转换成弧度制。
      输入和输出都是 `f64` 。没必要传入 `&f64` ，因为复制 `f64` 花销很小。
      但是使用 `into_radians` 名称就会具有误导性，因为输入数据没有被消耗。
- `into_`
    - [`String::into_bytes()`](https://doc.rust-lang.org/std/string/struct.String.html#method.into_bytes)
      从 `String` 提取出背后的 `Vec<u8>` 数据，这是无代价的。
      它转移了 `String` 的所有权，然后返回具有所有权的 `Vec<u8>` 。
    - [`BufReader::into_inner()`](https://doc.rust-lang.org/std/io/struct.BufReader.html#method.into_inner)
      转移了 buffered reader 的所有权，取出其背后的 reader ，这是无代价的。
      存于缓冲区的数据被丢弃了。
    - [`BufWriter::into_inner()`](https://doc.rust-lang.org/std/io/struct.BufWriter.html#method.into_inner)
      转移了 buffered writer 的所有权，取出其背后的 writer ，这可能以很大的代价刷新所有缓存数据。

如果类型转换方法返回的类型具有 `mut` 修饰，那么这个方法的名称应如同返回类型组成部分的顺序那样，带有 `mut` 。
比如 [`Vec::as_mut_slice`](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.as_mut_slice) 返回 `&mut [T]` 类型，这个方法的功能正如其名称所述，所以这个名称优于 `as_slice_mut` 。

其他参考示例：

- [`Result::as_ref`](https://doc.rust-lang.org/std/result/enum.Result.html#method.as_ref)
- [`RefCell::as_ptr`](https://doc.rust-lang.org/std/cell/struct.RefCell.html#method.as_ptr)
- [`slice::to_vec`](https://doc.rust-lang.org/std/primitive.slice.html#method.to_vec)
- [`Option::into_iter`](https://doc.rust-lang.org/std/option/enum.Option.html#method.into_iter)


**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | Lint Level |
| ------ | ---- | --------- | ------ | ------ |
| [wrong_self_convention](https://rust-lang.github.io/rust-clippy/master/index.html#wrong_self_convention) | yes| no | Style | warn |
