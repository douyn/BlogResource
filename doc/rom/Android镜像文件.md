## Android系统镜像文件
在编译完aosp之后，在out/target/product/[YOUR_PRODUCT_NAME]/目录下生成最终要烧入到设备的镜像文件，包括system.img,userdata.img,ramdisk.img等，下表是各种Android中常见的镜像文件及对应的功能:

Image | Description
-|-
boot.img | 包括内核启动参数，内核等多个元素
system.img | Android系统的运行程序包，被挂在到设备的/system节点下
ramdisk.img | 小型文件系统，是Android系统启动的关键
userdata.img | 各程序的数据惩处所造，被挂在到设备的/data目录下
recovery.img | 设备进入"recovery"模式所需要的镜像文件
cache.img | 缓冲区，被挂载到/cache节点
misc.img | 包括各种杂项资源

#### 1. boot.img
理解boot.img的最好方法就是学习它的制作工具-mkbootimg,路径是在 system/core/mkbootimg中。

mkbootimg的语法规则如下:
> mkbootimg --kernel <filename> --ramdisk <filename>  [--second <2ndbootloader-filename>] [ --cmdline <kernel-commandline> ] [ --board <boardname> ] [ --base <address> ] [ --pagesize <pagesize> ] -o|--output <filename>
> --kernel: 指定内核程序包的存放路径
> --ramdisk: 指定ramdisk.img的存放路径
> --second: 可选， 指定第二阶段启动文件
> --cmdline: 可选，内核启动参数
> --board: 可选，版名称
> --base: 可选，内核启动基地址
> --pagesize: 可选，页大小
> --output: 输出名称

boot.img的文件结构：

1. boot header: 内核启动参数等信息，占据1page
2. kernel: 内核程序是整个Android系统的基础，n pages = (kernel_size + page_size - 1) / page_size
3. ramdisk: 小型文件系统
4. second stage: 这个元素通常用于扩展功能

#### 2. system.img
包含设备/system节点中的相关内容
#### 3. ramdisk.img
存放root根目录的镜像，并且在Android的启动过程中发挥重要的作用