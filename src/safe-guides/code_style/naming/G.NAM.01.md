## G.NAM.01 标识符命名应该符合阅读习惯

**【级别】** 要求

**【描述】**

标识符的命名要清晰、明了，有明确含义，容易理解。符合英文阅读习惯的命名将明显提高代码可读性。

一些好的实践包括但不限于：

- 使用正确的英文单词并符合英文语法，不要使用拼音
- 仅使用常见或领域内通用的单词缩写
- 布尔型变量或函数避免使用否定形式，双重否定不利于理解
- 不要使用 Unicode 标识符。

**【反例】**

```rust
let ming: &str = "John";
let xing: &str = "Smith";
const ERROR_NO_1: u32 = 336;
const ERROR_NO_2: u32 = 594;

fn not_number(s:&str) -> bool {/* ... */}
```

**【正例】**

```rust
let first_name: &str = "John";
let last_name: &str = "Smith";
const ERROR_DIRECTORY_NOT_SUPPORTED: u32 = 336;
const ERROR_DRIVER_CANCEL_TIMEOUT: u32 = 594;

fn is_number(s:&str) -> bool {/* ... */} //用来判断是否为整数
```


**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制化参考】

检测错误的英文拼写，检测出后提示；检测拼音，检测出来提示。拼写错误可参考 [client9/misspell](https://github.com/client9/misspell) 。