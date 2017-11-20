# 【学习笔记】Android热修复
*[未完待续]*

[原文链接-论热修复实战心得—很值得学习的热修复详解|强烈推荐](https://mp.weixin.qq.com/s?__biz=MzI3OTU0MzI4MQ==&mid=100001255&idx=1&sn=6b1674c7578039b61b4c34825619c363&chksm=6b4769795c30e06fa1d02f89e7a3e230c2d9c5761b0256fd1ed33eee899803a95f574a144450#rd)

## 1. 简述
热修复的概念。

两种热修复方案：
* 阿里系： DeXposed/AndFix，从底层二进制入手
* 腾讯系: Tinker,从java类加载机制入手 

这篇文章的主题是基于类加载机制研究热修复的原理和实现。

## 2. Android中如何动态修复bug
一般是要通过下边的步骤：
1. 从服务器上下载补丁文件(.class文件)
2. 让这些.class文件被调用

在第二步中，要实现被调用就要使用Android的类加载器，通过类加载器加载这些修复好的class，覆盖有问题的class，理论上就能修复bug。

## 3. Android中的类加载器
java的JVM是通过ClassLoader来加载应用中的class，Android使用的是对jvm进行优化过的dalvik，并且class文件会被打包到一个dex文件中。在Android中要加载dex文件中的class需要用到PathClassLoader或者DexClassLoader这两个专用的类加载器。
1. 查看源码
2. PathClassLoader和DexClassLoader的区别
    * PathClassLoader只加载已经安装到手机上的apk文件目录下的类，是Android默认的类加载器
    * DexClassLoader可以加载任意目录下的dex/zip/apk/jar文件。

3. BaseClassLoader

PathClassLoader和DexClassLoader都继承自BaseClassLoader。


