# H.Rust编译器编译参数说明

## Rustc 说明

通过以下命令可以打印编译器相关参数选项：

```rust

$ rustc -h 
// 或
$ rustc --help
// 在 Nightly Rust 下，比 Stable Rust 多一个 `-Z` 参数，用于传递 unstable compiler options
```

## 参数功能

### `-h` / `--help`

用于打印 `rustc` 的帮助信息

### `--cfg` 

用于打开或关闭 `#[cfg]` 变量中的各种条件编译设置。

```rust
--cfg 'verbose'  // 对应 #[cfg(verbose)]
// or
--cfg 'feature="serde"' // 对应 #[cfg(feature = "serde")]
```

### `-L`

`-L` 用于将一个目录添加到外部搜索路径。命令格式如下：

```rust
-L [KIND=]PATH
```

当 `KIND` 是以下情况之一时，可以使用 `-L KIND=PATH `这种形式指定搜索路径的类型：

- `dependency` — 仅在该目录中搜索传递依赖项。
- `crate` — 仅在该目录中搜索此 crate 的直接依赖项。
- `native` — 仅在该目录中搜索本地库。
- `framework` — 仅用于在该目录中搜索 macOS 框架。
- `all` — 搜索此目录中的所有库类型。如果 KIND 没有指定，这将是默认值.

### `-l`

用于将生成的 crate 链接到本地库。

```rust
-l [KIND[:MODIFIERS]=]NAME[:RENAME]
```

当 `KIND` 是以下情况之一时，库的种类可以使用 `-l KIND=lib` 这种形式指定:

- `dylib` — 本地动态库
- `static` — 本地静态库 (例如 .a archive 文件)
- `framework` — macOS 框架

可以用`#[link]`属性指定库的种类。 如果未在 `link `属性或命令行中指定 `KIND` ，它将链接到可用动态库，否则将使用静态库。 如果在命令行中指定了库类型，其将会覆盖 `link` 属性指定的库类型。

`link` 属性中使用的名称可以使用形如` -l ATTR_NAME:LINK_NAME` 形式覆盖，其中 `ATTR_NAME` 是 `link` 属性中的名称，`LINK_NAME `是将要链接到的实际库的名称。

#### `--crate-type` 

这将指示 rustc 以何种 crate type 去构建。该 Flag 接收逗号分隔的值列表，也可以多次指定。有效的 crate type 如下：

- `lib` — 编译器生成的首选库类型， 目前默认为 rlib。
- `rlib` — Rust 静态库。
- `staticlib` — 本地静态库。
- `dylib` — Rust 动态库。
- `cdylib` — 本地动态库。
- `bin` — 可执行程序。
- `proc-macro` — 生成格式化且编译器可加载的过程宏库。

可以使用 `crate_type`属性来指定 crate 类型，注意  `--crate-type` 命令行的值会覆盖 crate_type 属性的值。更多细节可以参阅 reference 中的[ 链接章节](https://doc.rust-lang.org/stable/reference/linkage.html)。

#### `--crate-name`

用于指定正在构建的 crate 名称

#### `--edition`

用于指定编译器使用哪个 版次（edition），可选项 `2015|2018|2021`。

#### `--emit`

```rust
 --emit [asm|llvm-bc|llvm-ir|obj|metadata|link|dep-info|mir]
```

该 Flag 控制编译器生成的输出文件的类型。其接收以逗号分隔的值列表，也可以多次指定。有效的生成类型有：

- `asm` — 生成在 crate 中的一个汇编代码文件。 默认的输出文件是 CRATE_NAME.s。
- `dep-info` — 生成一个包含 Makefile 语法的文件，指示加载以生成 crate 的所有源文件。 默认输出文件是 CRATE_NAME.d。
- `link` — 生成由 --crate-type 指定的 crates 。 默认输出文件取决于平台和 crate 类型。 如果未指定 --emit 这将是默认值。
- `llvm-bc` — 生成一个包含 LLVM bitcode 的二进制文件。默认输出文件是 CRATE_NAME.bc。
- `llvm-ir` — 生成一个包含 LLVM IR（ LLVM 中间语言）的文件。默认的输出文件是 CRATE_NAME.ll。
- `metadata` — 生成一个关于该 crate 的元数据的文件。 默认输出文件是 CRATE_NAME.rmeta。
- `mir` — 生成一个包含 Rust 中级中间表示（即中级中间语言）的文件。默认输出文件名是 `CRATE_NAME.mir`。
- `obj` — 生成一个本地对象文件，默认输出文件是 `CRATE_NAME.o`。

输出文件名可以用 `-o flag` 进行设置。使用` -C extra-filename`。Flag 可以添加文件名后缀。文件将被写入当前目录除非使用` --out-dir flag ` 标签。 每一个生成类型也可以使用 `KIND=PATH `的形式指定输出文件名，它优先于 `-o `标签。

### `--print`

用于打印有关编译器的各种信息。指定` --print `标签通常会禁用 `--emit `步骤且只打印请求的信息。打印的有效值类型为：

- `crate-name` — crate 的名称。
- `file-names` — 文件名由 link 命令执行的种类所决定。
- `sysroot` — 系统根目录路径，即`.rustup` 下的 `toolchains` 文件夹。
- `target-libdir` - 目标` lib `文件夹路径（同上）。
- `cfg` — 条件编译值列表。 了解更多条件编译值信息，请参阅 条件编译。
- `target-list` — 已知目标列表。 可以使用`--target `标签选择目标。
- `target-cpus `— 当前目标的可用 CPU 值列表。可以使用 `-C target-cpu=val`  Flag 选择目标。
- `target-features`— 当前目标的可用 目标 `features` 列表。目标 `features` 可以使用` -C target-feature=val `flag 启用。该Flag是不安全（ unsafe ）的。
- `relocation-models` — 重定位模型列表。重定位模型可以用 `-C relocation-model=val ` Flag 选择。
- `code-models` — 代码模型列表。代码模型可以用` -C code-model=val` flag 进行设置。
- `tls-models` — 支持的线程本地存储模型列表。 模型可以用` -Z tls-model=val ` Flag 来选择（仅限 Nightly Rust）。
- `native-static-libs` — 当创建一个` staticlib crate` 类型时可以使用此选项。 如果这是唯一的标志，它将执行一个完整的编译，并包含一个指出链接生成静态库时要使用的链接器 Flag 的诊断说明。该说明以文本 `native-static-libs:` 开始，以便更容易获取输出信息。

### `-g`

等价于 `-C debuginfo=2`，用于输出调试信息。

**调试信息说明：**

- `0`：根本没有调试信息（默认）。
- `1`: 仅行表。
- `2`：完整的调试信息。

### `-O` （大写的字母o，优化）

等价于 `-C opt-level=2`，用于编译优化，level为2。

**优化级别说明：**

- `0`：没有优化，也打开 [`cfg(debug_assertions)`](https://doc.rust-lang.org/rustc/codegen-options/index.html#debug-assertions)（默认）。
- `1`: 基本优化。
- `2`: 一些优化。
- `3`: 所有优化。
- `s`:  优化二进制大小。
- `z`：优化二进制大小，但也关闭循环向量化。 

默认值为`0`.

### `-o` 

用于控制输出的文件名。

#### `--out-dir DIR` 

用于指定输出目录位置。

#### `--explain OPT`

用于提供错误消息的详细说明。`rustc` 对于每一个（检测到的）错误都会返回一个错误码，这将打印给定错误的更详细说明。

#### `--test`

构建测试工具。

#### `--target TARGET`

指定编译的` target triple` 。

### 设置 lint

#### `-W` 

等价于 `--warn LINT` ，设置给定 lint 为 warning 级别。

#### `-A` 

等价于 `--allow LINT` ，设置给定 lint 为 allow 级别。

#### `-D` 

等价于 `--deny LINT` ，设置给定 lint 为 deny 级别。

#### `-F` 

等价于 `--forbid LINT` ，设置给定 lint 为 forbid 级别。

### `-C`

用于设置 [代码生成](https://doc.rust-lang.org/rustc/codegen-options/index.html) 选项。可以通过 `rustc -C help` 进一步查看其选项。

#### 已弃用参数

- `ar=val`，废弃。
- `no-stack-check=val`，废弃。

#### `code-model` （优化相关）

`code-model=val`，支持的值为：

- `tiny` - 微小的代码模型。
- `small`- 小代码模型。这是大多数受支持目标的默认模型。
- `kernel` - 内核代码模型。
- `medium` - 中等代码模型。
- `large` - 大型代码模型。比如 x86 平台上，告诉编译器不要进行任何假设，使用64位绝对取址模型访问代码及数据。

可以通过命令 `rustc --print code-models` 查看。

代码模型对程序及其符号可能使用的地址范围施加了限制。对于较小的地址范围，机器指令可能能够使用更紧凑的寻址模式。具体范围取决于目标架构和可用的寻址模式。

代码模型是程序员与编译器间的一个正式的协议，其中程序员表达其对最终程序将进入的当前正在编译的目标文件大小的意愿。比如，“不要担心，这个对象只会进入不那么大的程序，因此你可以使用快速的RIP相对取址模式”，相反，“这个对象期望链接进巨大的程序，因此请使用慢但安全的，带有完整64位偏移的绝对取址模式”。

#### `codegen-units` （优化： 性能 vs 编译速度）

`codegen-units=val`，这个值表示控制 crate 分成多少个代码生成单元（codegen units），它需要一个大于 0 的整数。

当一个 crate 被拆分为多个代码生成单元时，LLVM 能够并行处理它们。增加并行度可能会**加快编译时间**，但也可能会**产生更慢的代码**，因为可能会影响内联（inline）优化。

将此设置为 `1` 可能会提高生成代码的性能，但编译速度可能会变慢。

如果未指定，则默认值为 16，用于非增量构建。对于增量构建，默认值为 256，这允许缓存更细粒度。

#### `control-flow-guard` （安全）

此Flag 控制 LLVM 是否启用 Windows [控制流保护（Control Flow Guard）](https://docs.microsoft.com/en-us/windows/win32/secbp/control-flow-guard) 平台安全功能。对于非 Windows 目标，此标志当前被忽略。它采用以下值之一：

- `y`, `yes`, `on`, `checks`, 或无值：表示启用控制流防护。
- `nochecks`：在没有运行时强制检查的情况下发出控制流保护元数据（这应该只用于测试目的，因为它不提供安全强制）。
- `n`, `no`, `off`: 不启用控制流保护（默认）。

关于 Rust 和 CFG 的更详细说明参考：

- [Control Flow Guard for Clang/LLVM and Rust](https://msrc-blog.microsoft.com/2020/08/17/control-flow-guard-for-clang-llvm-and-rust/) 
- [UnStable book: control-flow-guard](https://doc.rust-lang.org/beta/unstable-book/compiler-flags/control-flow-guard.html)

#### `debug-assertions`

`debug-assertions=val`，用于打开或关闭 `cfg(debug_assertions)` 条件编译。采用以下的值：

- `y`, `yes`, `on`, 或无值：启用调试断言。
- `n`, `no`, or `off`: 禁用调试断言。

如果无指定，调试断言会在 [opt-level](https://doc.rust-lang.org/rustc/codegen-options/index.html#opt-level) 为 0 的优化级别下默认开启。所以这里需要注意优化级别，debug_assertions 在 release 编译时最好不要开启。这个和 `debug_assert!` 宏相关。

#### `default-linker-libraries`

`default-linker-libraries=val`，用于设置链接器是否可以链接它的默认库，可以设置的值为：

- `y`, `yes`, `on`, 或无值：包括默认库（默认）。
- `n`, `no`, or `off`: 排除默认库。

#### `embed-bitcode` （优化：编译大小 && 编译时间）

`embed-bitcode=val`，控制编译器是否将 LLVM 位码嵌入到目标文件中。它采用以下值之一：

- `y`, `yes`, `on`, 或无值：将位码放入 rlibs（默认）。
- `n`, `no`, or `off`: 从 rlibs 中省略位码。

`rustc` 执行链接时优化 (LTO) 时需要 LLVM 位码（bitcode）。嵌入的位码将出现在 rustc 生成的目标文件中，该文件的名称由目标平台定义。大多数时候是这样 `.llvmbc`。

如果你的编译实际不需要位码，使用`-C embed-bitcode=no` 可以显著提高编译时间并减少生成的文件大小（例如，如果你不为iOS编译或者你不执行LTO）。由于这些原因，Cargo尽可能地使用`-C embed-bitcode=no`。同样地，如果你直接用 rustc 构建，我们建议在不使用 LTO 时使用 `-C embed-bitcode=no`。

如果与`-C lto`结合，`-C embed-bitcode=no`将导致`rustc`在启动时中止，因为这种结合是无效的。

> bitcode是由LLVM引入的一种中间代码(Intermediate Representation，简称IR)，它是源代码被编译为二进制机器码过程中的中间表示形态，它既不是源代码，也不是机器码。从代码组织结构上看它比较接近机器码，但是在函数和指令层面使用了很多高级语言的特性。LLVM 优化器负责对bitcode进行各种类型的优化，将bitcode代码进行一些逻辑等价的转换，使得代码的执行效率更高，体积更小，比如DeadStrip/SimplifyCFG，LLVM 后端负责把优化后的bitcode编译为指定目标架构的机器码。
>
> 来源：[https://xelz.info/blog/2018/11/24/all-you-need-to-know-about-bitcode/](https://xelz.info/blog/2018/11/24/all-you-need-to-know-about-bitcode/)

#### `force-frame-pointers` （优化：性能）

`force-frame-pointers=val`，用于设置是否强制启用 帧指针（frame-pointers）。相当于 Clang 的`-fno-omit-frame-pointer`，

它采用以下值之一：

- `y`, `yes`, `on`, 或无值：强制启用帧指针。
- `n`, `no`, or `off`: 不要强制启用帧指针。这并不一定意味着将删除帧指针。

如果未强制启用帧指针，则默认行为取决于 target。

一般情况下，如果设置 `force-frame-pointers=no` 是一种帧指针省略优化。它造成的弊大于利，默认情况下不应启用。

#### `force-unwind-tables` （优化：编译大小）

`force-unwind-tables=val`，强制生成 unwind 表。它采用以下值之一：

- `y`, `yes`, `on`, 或无值：强制生成 unwind 表。
- `n`, `no`, or `off`: 不强制生成 unwind 表。如果目标需要 unwind 表，则会发出错误。

如果未指定，则默认值取决于 target 。

打开 `force-unwind-tables=on` 可能会导致二进制编译大小膨胀，对于移动和嵌入式这种二进制大小很重要的场景下，建议启用该选项。

#### `incremental`（优化：编译时间）

`incremental=val` ，用于启用增量编译。

#### `inline-threshold` （优化：性能）

`inline-threshold=val`， 允许您设置内联函数的默认阈值。它接受一个无符号整数作为值。内联基于成本模型（cost model），其中较高的阈值将允许更多内联。

默认值取决于[opt-level](https://doc.rust-lang.org/rustc/codegen-options/index.html#opt-level)：

| 选择级别 | 临界点                                             |
| -------- | -------------------------------------------------- |
| 0        | 不适用，仅内联始终内联函数                         |
| 1        | 不适用，仅内联始终内联函数和 LLVM 生命周期内在函数 |
| 2        | 225                                                |
| 3        | 275                                                |
| s        | 75                                                 |
| z        | 25                                                 |

#### 链接相关参数

- `link-arg=val`，将单个额外参数附加到链接器调用。
- `link-args=val`，将多个额外参数附加到链接器调用。选项应该用空格分隔。
- `link-dead-code=val`，控制链接器是否保留死代码，尝试构建代码覆盖率指标时，此标志可能有用。它采用以下值之一：
  - `y`, `yes`, `on`, 或无值：保留死代码。
  - `n`, `no`, or `off`: 删除死代码（默认）。
- ` link-self-contained=val`，控制链接器是使用 Rust 附带的库和对象还是系统中的库和对象。
- `linker=val `，控制链接器`rustc`调用哪个链接器来链接您的代码。它采用链接器可执行文件的路径。如果未指定此标志，则将根据目标推断链接器。选择特定的链接器，有助于**优化编译时间**。
- `linker-flavor=val`，链接器带有[`-C linker`标志](https://doc.rust-lang.org/rustc/codegen-options/index.html#linker)，则链接器风格是从提供的值中推断出来的。如果没有给出链接器，则使用链接器风格来确定要使用的链接器。每个`rustc`目标都默认为某种链接器风格。选项参考:[linker-flavor](https://doc.rust-lang.org/rustc/codegen-options/index.html#linker-flavor) 。
- `linker-plugin-lto=val`，允许将 LTO 优化推迟到实际的链接步骤，如果所有被链接的目标文件都是由基于 LLVM 的工具链创建的，那么这反过来又允许跨编程语言边界执行过程间**优化**。详细介绍参见[Linker-plugin-LTO](https://doc.rust-lang.org/rustc/linker-plugin-lto.html)。它采用以下值之一：
  - `y`, `yes`, `on`, 或无值：启用链接器插件 LTO。
  - `n`, `no`, or `off`: 禁用链接器插件 LTO（默认）。
  - 链接器插件的路径。

#### `lto` （优化：性能）

`lto=val`，这个标志控制LLVM是否使用链接时优化（link-time optimizations），以产生更好的优化代码，使用整个程序分析，代价是延长链接时间，所以，会减慢编译时间。它取以下值之一：

- `y`, `yes`,` on`,` fat`, or `no`值：执行 "fat "LTO，试图在依赖图中的所有crate中进行优化。
- `n`,` no`,` of`f: 禁用LTO。
- `thin`：执行 "Thin "LTO。这与 "fat "类似，但运行时间大大减少，同时仍能实现与 "fat "类似的性能提升。

如果没有指定`-C lto`，那么编译器将尝试执行 "thin local LTO"，它只在本地的crate上对其代码单元执行 "thin "LTO。当没有指定`-C lto`时，如果代码单元是`1`或者优化被禁用（`-C opt-level=0`），LTO将被禁用。

即：

- 当没有指定`-C lto`时:
  - `codegen-units=1`: 禁用LTO
  - `opt-level=0`: 禁用LTO。
- 当`-C lto`被指定时：
  - `lto`：16个代码生成单元，在整个crate 中执行 fat LTO。
  - `codegen-units=1` +` lto`:  `1`个编码生成单元，跨 crate 进行fat LTO。

跨语言的LTO 参见[Linker-plugin-LTO](https://doc.rust-lang.org/rustc/linker-plugin-lto.html) 。

#### `passes` （优化：性能）

`passes=val`，设置额外的 LLVM passes 列表，使用空格分隔。

> LLVM Pass 是LLVM代码优化（optimization）中的一个重要组成部分。为便于理解，可以将Pass看作一个又一个的模块，各个Pass可以通过IR获取信息为下一个Pass做好准备，又或者直接对中间代码进行优化。
>
> 总的来说，所有的pass大致可以分为两类：
>
> - 分析(`analysis`)和转换分析类的pass以提供信息为主
> - 转换类(`transform`)的pass优化中间代码
>
> 详细参考：[代码优化与LLVM IR pass](https://kiprey.github.io/2020/06/LLVM-IR-pass/)

相关选项：`no-prepopulate-passes=val`，使用空的 Pass 列表。



#### `no-redzone`

`no-redzone=val`，允许禁用[红区](https://en.wikipedia.org/wiki/Red_zone_(computing))。它采用以下值之一：

- `y`, `yes`, `on`, 或无值：禁用红色区域。
- `n`, `no`, 或`off`: 启用红色区域。

> **红区**（redzone）是System V ABI提供的一种优化的产物，它允许函数无需调整**栈指针**（stack pointer），便能临时使用其**栈帧**（stack frame）下方的128个字节。红区被定义为调整过的栈指针下方128个字节的区域——函数将会使用这个区域，来存放一些无需跨越函数调用的临时数据。因此，在一些情况下，比如在小的**叶函数**（leaf function）[1]中，我们可以优化掉调整栈指针所需的两条指令。(
>
> **叶函数**（leaf function）指的是不调用其它函数的函数；可以理解为，是函数调用图的叶子节点。特别地，**尾递归函数**（tail recursive function）的尾部可以看作是叶函数。
>
> 然而，当**异常**（exception）或**硬件中断**（hardware interrupt）发生时，这种优化却可能产生严重的问题。
>
> 参考：[使用Rust编写操作系统（附录二）：禁用红区](https://github.com/rustcc/writing-an-os-in-rust/blob/master/appendix-b-red-zone.md)

#### `no-vectorize-loops` （优化：性能）

`no-vectorize-loops=val`，禁用循环矢量化（loop vectorization optimization passes）。等价于 Clang 的 `-fno-vectorize`。

LLVM Loop Vectorizer 具有许多功能，可以对复杂的循环进行矢量化（向量化即“批量操作”，数据并行）。循环矢量化器使用成本模型来决定最佳矢量化因子和展开因子。但是，矢量化器的用户可以强制矢量化器使用特定值。许多循环无法向量化，包括具有复杂控制流、不可向量化类型和不可向量化调用的循环。对于更复杂的情况，我们则需要手动进行SIMD编程。

是否禁用，取决于你的使用场景。

相关：`no-vectorize-slp=val`，禁用 SLP 向量化。

#### ` overflow-checks` （安全）

` overflow-checks=val `，控制[运行时整数溢出检查](https://doc.rust-lang.org/reference/expressions/operator-expr.html#overflow)，启用溢出检查时，溢出时会发生恐慌。取值为：

采用以下值之一：

- `y`, `yes`, `on`, 或无值：启用溢出检查。
- `n`, `no`, or `off`: 禁用溢出检查。

如果未指定，则在启用 [调试断言](https://doc.rust-lang.org/rustc/codegen-options/index.html#debug-assertions)时启用溢出检查，否则禁用。

#### `panic`

控制代码恐慌时发生的情况:

- `abort`: 恐慌时终止进程，不能执行析构函数。
- `unwind`: 恐慌时展开堆栈，可以执行析构函数，以及 catch_unwind 。

如果未指定，则默认值取决于目标。



#### ` prefer-dynamic`

` prefer-dynamic=val`，默认情况下，`rustc`更喜欢静态链接依赖项。如果库的静态和动态版本都可用，此选项将指示应尽可能使用动态链接。有一个内部算法用于确定是否可以静态或动态地与依赖项链接。例如，`cdylib` crate类型可能只使用静态链接。此标志采用以下值之一：

- `y`, `yes`, `on`, 或无值：使用动态链接。
- `n`, `no`, or `off`: 使用静态链接（默认）。

#### `profile-generate` （优化）

`profile-generate=val`，允许创建检测二进制文件，这些二进制文件将收集分析数据以用于 Profile Guided Optimization (PGO)。该Flag 采用一个可选参数，该参数是已检测二进制文件将向其中发出收集的数据的目录的路径。有关详细信息，请参阅 [profile-guided optimization](https://doc.rust-lang.org/rustc/profile-guided-optimization.html) 。

相关：`profile-use=val`。

#### `split-debuginfo` （调试）

`split-debuginfo=val `，控制`rustc`生成的调试信息的“split debuginfo”的发射。此选项的默认行为是特定于平台的，并非此选项的所有可能值都适用于所有平台。可能的值为：

- `off`- 这是具有 ELF 二进制文件和 windows-gnu（不是 Windows MSVC 和 macOS）的平台的默认设置。这通常意味着可以在可执行文件部分的最终工件中找到 DWARF 调试信息。Windows MSVC 不支持此选项。在 macOS 上，此选项可防止最终执行`dsymutil`生成调试信息。
- `packed`- 这是 Windows MSVC 和 macOS 的默认设置。这里的“packed”一词意味着所有调试信息都打包到与主可执行文件不同的文件中。在 Windows MSVC 上这是一个`*.pdb`文件，在 macOS 上这是一个`*.dSYM`文件夹，而在其他平台上这是一个`*.dwp` 文件。
- `unpacked`- 这意味着调试信息将在每个编译单元（目标文件）的单独文件中找到。Windows MSVC 不支持此功能。在 macOS 上，这意味着原始目标文件将包含调试信息。在其他 Unix 平台上，这意味着`*.dwo`文件将包含调试信息。

请注意，此时`packed`和`unpacked`在`-Z unstable-options`非 macOS 平台上被关闭。

#### `strip` （优化：编译大小）

`strip=val`，控制在链接期间从二进制文件中剥离调试信息和类似的辅助数据。可用于减少编译文件大小。

此选项支持的值为：

- `none`- 根据目标将调试信息和符号（如果存在）复制到生成的二进制文件或单独的文件中（例如`.pdb`，MSVC 中的文件）。
- `debuginfo` - debuginfo 部分和符号表部分中的 debuginfo 符号在链接时被剥离，并且不会复制到生成的二进制文件或单独的文件中。
- `symbols`- 与 相同`debuginfo`，但如果链接器支持，符号表部分的其余部分也会被剥离。

### `-Z`

`-Z` Flag 只允许在 Nightly Rust 下使用，因为它包含了 未稳定的编译器选项。

在该 Flag 下面有许多未稳定的子参数，这里就不一一列举。只挑选几个和安全相关的展示一下：

- `stack-protector=val` ，用于控制栈粉碎保护策略，用于缓冲溢出保护。通过命令 `rustc --print stack-protector-strategies`可以看到详细设置值。
- `sanitizer=val`，sanitizers（CFI 除外）的实现几乎完全依赖于 LLVM，将来增加 GCC 后端应该可以多一些支持。用于支持 内存错误检测器（[AddressSanitizer](https://clang.llvm.org/docs/AddressSanitizer.html)）、LLVM 控制流完整性 (CFI) 提供前沿控制流保护（[ControlFlowIntegrity](https://clang.llvm.org/docs/ControlFlowIntegrity.html) ）、运行时内存泄漏检测器（[LeakSanitizer](https://clang.llvm.org/docs/LeakSanitizer.html)）、未初始化读取的检测器（[MemorySanitizer](https://clang.llvm.org/docs/MemorySanitizer.html)）、数据竞争检测器（[ThreadSanitizer](https://clang.llvm.org/docs/ThreadSanitizer.html)）等。



## 参考

https://doc.rust-lang.org/rustc/command-line-arguments.html

https://doc.rust-lang.org/rustc/codegen-options/index.html


