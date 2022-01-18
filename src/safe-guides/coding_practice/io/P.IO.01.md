## P.IO.01  使用 `read_to_end/read_to_string`方法时注意文件的大小能否一次性读入内存中

**【描述】**

对于内存可以一次性读完的文件，可以使用 `read_to_end/read_to_string`之类的方法。但是如果你想读任意大小的文件，则不适合使用它们。
