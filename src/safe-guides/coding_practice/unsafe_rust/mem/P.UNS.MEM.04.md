## P.UNS.MEM.04  尽量用可重入(reentrant)版本的 C-API 或系统调用

**【描述】**

以 Linux 系统为例，在 **glibc**(/usr/lib/libc.so) 等知名 C 语言库中，很多 API 会既提供不可重入版本和**可重入(reentrant)**版本，例如 ctime 和 ctime_r 这对系统调用。可重入版本的函数命名一般带 **_r** 的后缀，*_r* 也就是单词可重入 reentrant 的缩写。

> libc 中不可重入函数的执行过程一般是将函数的输出写到动态库的某个 static 命令内，然后再返回指向该 static 变量的指针返回给调用方，因此是一种「有状态」的函数，多线程环境下可能有**线程安全问题**。

使用不可重入函数的风险会导致开发人员带来很大的心智负担，需要耗费人力进行代码安全评审确保没有线程安全和内存安全问题，因此必须尽量使用可重入版本的函数。

**【反例】**

`ctime`, `gmtime`,` localtime`, `gethostbyname`

**【正例】**

`chrono` 库中用 `libc::localtime_r` 获取本地时间而不用 `libc::localtime`。

还有诸如 `ctime_r`, `gmtime_r`,` localtime_r`, `gethostbyname_r`等。
