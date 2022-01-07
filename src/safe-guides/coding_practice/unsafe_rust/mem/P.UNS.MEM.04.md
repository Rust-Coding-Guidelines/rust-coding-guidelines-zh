## P.UNS.MEM.04  尽量用可重入(reentrant)版本的 C-API 或系统调用

**【描述】**

以 Linux 系统为例，在 **glibc**(/usr/lib/libc.so) 等知名 C 语言库中，

很多 API 会既提供不可重入版本和**可重入(reentrant)**版本，例如 ctime 和 ctime_r 这对系统调用。

可重入版本的函数命名一般带 **_r** 的后缀，*_r* 也就是单词可重入 reentrant 的缩写。

libc 中不可重入函数的执行过程一般是将函数的输出写到动态库的某个 static 命令内，然后再返回指向该 static 变量的指针返回给调用方，因此是一种「有状态」的函数，多线程环境下可能有**线程安全问题**。

例如线程 A 正在将 glibc 动态库的 gmtime 数据逐个复制回来，结果复制到一半线程 B 调用 gmtime 把后半部分的 gmtime 输出数据给更新掉了导致线程 A 得到的数据有误。

而无重入版本例如 libc::localtime_r 会比 libc::localtime 多一个入参叫 result，

允许调用方进程的内存空间内分配内存，再将调用方进程的可变指针传入到 glibc 中让 glibc 修改可知指针指向的数据。

应当通过工具搜索动态库的函数符号查找可重入版本的函数，或者通过 man 文档查询自己所用函数有没有可重入的版本。

```
[w@ww repos]$ nm -D /usr/lib/libc.so.6 | grep "_r@"
00000000000bb030 W asctime_r@@GLIBC_2.2.5
00000000000bb100 T ctime_r@@GLIBC_2.2.5
0000000000040a30 T drand48_r@@GLIBC_2.2.5
```

使用不可重入函数的危害例如 P.UNS.MEM.02 和 P.UNS.MEM.03 规范的反例中的 sqlite3_libversion() 会导致开发人员带来很大的心智负担，需要人工 code review 确保没有线程安全和内存安全问题，因此必须尽量使用可重入版本的函数。

**【反例】**

`ctime`, `gmtime`,` localtime`, `gethostbyname`

**【正例】**

`chrono` 库中用` libc::localtime_r` 获取本地时间而不用` libc::localtime`

`ctime_r`, `gmtime_r`,` localtime_r`, `gethostbyname_r`
