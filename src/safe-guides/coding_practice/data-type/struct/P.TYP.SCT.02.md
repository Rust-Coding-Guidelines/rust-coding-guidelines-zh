## P.TYP.SCT.02  结构体实例需要默认实现时，宜使用`Default`特质

**【描述】**

为结构体实现 `Default` 对于简化代码提高可读性很有帮助。

**【示例】**

 ```rust
 use std::{path::PathBuf, time::Duration};
 
 #[derive(Default, Debug, PartialEq)]
 struct MyConfiguration {
     output: Option<PathBuf>,
     search_path: Vec<PathBuf>,
     timeout: Duration,
     check: bool,
 }

 fn main() {
     // 使用 default 方法创建实例
     let mut conf = MyConfiguration::default();
     conf.check = true;
     println!("conf = {:#?}", conf);
         
     // 创建新实例的时候，使用局部更新更加方便
     let conf1 = MyConfiguration {
         check: true,
         ..Default::default()
     };
     assert_eq!(conf, conf1);
 }
 ```




