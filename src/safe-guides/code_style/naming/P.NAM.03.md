## P.NAM.03 标识符命名应该符合阅读习惯

**【描述】**

标识符的命名要清晰、明了，有明确含义，容易理解。符合英文阅读习惯的命名将明显提高代码可读性。

一些好的实践包括但不限于：

- 使用正确的英文单词并符合英文语法，不要使用拼音
- 仅使用常见或领域内通用的单词缩写
- 布尔型变量或函数避免使用否定形式，双重否定不利于理解
- 不要使用 Unicode 标识符

**【反例】**

```rust
// 不符合： 使用拼音
let ming: &str = "John";
let xing: &str = "Smith";
// 不符合： 含义不明确
const ERROR_NO_1: u32 = 336;
const ERROR_NO_2: u32 = 594;
// 不符合：函数名字表示的函数作用不明了
fn not_number(s:&str) -> bool {/* ... */}
```

**【正例】**

```rust
// 符合
let first_name: &str = "John";
let last_name: &str = "Smith";
const ERR_DIR_NOT_SUPPORTED: u32 = 336;
const ERR_DVER_CANCEL_TIMEOUT: u32 = 594;
// 符合
fn is_number(s:&str) -> bool {/* ... */} 
```