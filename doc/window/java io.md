java io

![IO流图解](http://img.blog.csdn.net/20140814122633546?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTUxMjU5MjE1MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

字节流和字符流的区别

* 字节流操作的基本单元是字节，字符流操作的基本单元是Unicode码元
* 字节流默认不适用缓冲区，字符流使用缓冲区
* 字节流通畅用于处理二进制数据，实际上它可以处理任意类型的数据，但他不支持直接读取和写入unicode码元，字符流通常处理文本数据，它支持写入以及读取Unicode码元

