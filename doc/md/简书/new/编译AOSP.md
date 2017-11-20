# 编译AOSP
## 1. 下载源码
如果可以翻墙的话，建议先到[Google官方网站](https://source.android.com/source/downloading?hl=zh-cn)上看一看。
1. 下载resp

		mkdir ~/bin
        PATH=~/bin:$PATH
        curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
        chmod a+x ~/bin/repo
        
        # 最近下载的时候发现国内翻墙也不能用google的源，所以推荐时候下边的源
        curl https://mirrors.tuna.tsinghua.edu.cn/git/git-repo > ~/bin/repo
2. 初始化和同步

    由于某墙的原因,这里我们采用国内的镜像源进行下载.
    目前,可用的镜像源一般是科大和清华的,具体使用差不多,这里我选择清华大学镜像进行说明.(参考:[科大源](https://lug.ustc.edu.cn/wiki/mirrors/help/aosp),[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/))

	###### 2.1 使用初始化包进行初始化和同步。
	因为源码很大，任何故障都会导致同步失败，所以建议使用初始化包进行初始化。初始化包的源为https://mirrors.tuna.tsinghua.edu.cn/aosp-monthly/aosp-latest.tar。 下载后记得肯局checksum.txt的内容校验一下。由于所有代码都是从隐藏的.repo目录中checkout出来的，所以我们只保留了.repo目录，下载后解压，再repo sync一遍即可得到完整的目录。
    也可以使用下边的命令
    	
        wget https://mirrors.tuna.tsinghua.edu.cn/aosp-monthly/aosp-latest.tar # 下载初始化包
        tar xf aosp-latest.tar
        cd AOSP   # 解压得到的 AOSP 工程目录
        # 这时 ls 的话什么也看不到，因为只有一个隐藏的 .repo 目录
        repo sync # 正常同步一遍即可得到完整目录
        # 或 repo sync -l 仅checkout代码
	###### 2.2 传统的初始化方法
建立目录

		mkdir source
        cd source
初始化仓库

		repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest
        
        # 如何你需要特定的Android版本
        repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-4.0.1_r1
同步源码

		repo sync
       
## 2. 搭建编译环境
建议先看一下[Google官方构建编译环境指南](https://source.android.com/source/initializing.html)。

1. 编译要求

	在下载和编译之前，请先确保你的操作系统符合以下要求。

	**硬件要求**
    
	您的开发工作站必须达到或超出以下硬件要求：
    
	* 如果是 Gingerbread (2.3.x) 及更高版本（包括 master 分支），需要使用 64 位环境。如果是较低的版本，则可以在 32 位系统中进行编译。
	* 如果是进行校验，至少需要 100GB 可用磁盘空间；如果是进行单次编译，至少需要 150GB 可用磁盘空间；如果是进行多次编译，至少需要 200GB 或更多可用磁盘空间。如果您使用 ccache，则需要更多空间。
	* 如果您在虚拟机中运行 Linux，则至少需要 16GB 的 RAM/交换空间。
	
    **软件要求**

	[Android 开放源代码项目 (AOSP)](https://android.googlesource.com/) master 分支历来是在 Ubuntu Long Term Support (LTS) 版本中进行开发和测试，但您也可以使用其他 Ubuntu 分发版本。要查看建议使用的版本，请参阅下面的列表。

	您的工作站必须具有下面列出的软件。要查看所需的其他程序包以及用于安装这些程序包的命令，请参阅[构建编译环境](https://source.android.com/source/initializing.html?hl=zh-cn)。

	**操作系统**

	Android 通常是在 GNU/Linux 或 Mac OS 操作系统中进行编译。您也可以使用虚拟机在不支持的系统（例如 Windows）中编译 Android。
    
	*GNU/Linux*

	* Android 6.0 (Marshmallow) - AOSP master：Ubuntu 14.04 (Trusty)
	* Android 2.3.x (Gingerbread) - Android 5.x (Lollipop)：Ubuntu 12.04 (Precise)
	* Android 1.5 (Cupcake) - Android 2.2.x (Froyo)：Ubuntu 10.04 (Lucid)
	
	*Mac OS (Intel/x86)*

	* Android 6.0 (Marshmallow) - AOSP master：Mac OS v10.10 (Yosemite) 或更高版本，具有 Xcode 4.5.2 和命令行工具
	* Android 5.x (Lollipop)：Mac OS v10.8 (Mountain Lion)，具有 Xcode 4.5.2 和命令行工具
	* Android 4.1.x-4.3.x (Jelly Bean) - Android 4.4.x (KitKat)：Mac OS v10.6 (Snow Leopard) 或 Mac OS X v10.7 (Lion)，以及 Xcode 4.2（Apple 的开发者工具）
	* Android 1.5 (Cupcake) - Android 4.0.x (Ice Cream Sandwich)：Mac OS v10.5 (Leopard) 或 Mac OS X v10.6 (Snow Leopard)，以及 Mac OS X v10.5 SDK
	
	**Java 开发套件 (JDK)**

	请注意，由于没有适用于 Ubuntu 14.04 的受支持 OpenJDK 8 程序包，因此您必须手动安装 Ubuntu 15.04 程序包。要查看具体说明，请参阅适用于 Ubuntu LTS 14.04 的 JDK。

	* AOSP 中 Android 的 master 分支：Ubuntu - OpenJDK 8；Mac OS - jdk 8u45 或更高版本
	* Android 5.x (Lollipop) - Android 6.0 (Marshmallow)：Ubuntu - OpenJDK 7；Mac OS - jdk-7u71-macosx-x64.dmg
	* Android 2.3.x (Gingerbread) - Android 4.4.x (KitKat)：Ubuntu - Java JDK 6；Mac OS - Java JDK 6
	* Android 1.5 (Cupcake) - Android 2.2.x (Froyo)：Ubuntu - Java JDK 5
	
    **主要程序包**

	* [python.org](http://www.python.org/download/) 中提供的 Python 2.6 - 2.7
	* [gnu.org](http://ftp.gnu.org/gnu/make/) 中提供的 GNU Make 3.81 - 3.82；对于 	Android 3.2.x (Honeycomb) 及更低版本，则需要将 make 3.82 还原到较低版本，以避免出现编译错误
	* [git-scm.com](http://git-scm.com/download) 中提供的 Git 1.7 或更高版本

	**设备二进制文件**

	下载预览、出厂映像、驱动程序、无线 (OTA) 更新和下列其他 Blob。如需更多详细信息，请参阅下载专有二进制文件。

	* [预览二进制文件 (Blob)](https://developers.google.com/android/nexus/blobs-preview?hl=zh-cn) - 用于 AOSP master 分支开发
	* [出厂映像](https://developers.google.com/android/nexus/images?hl=zh-cn) - 针对运行带标记的 AOSP 版本分支的受支持设备
	* [硬件支持二进制文件](https://developers.google.com/android/nexus/drivers?hl=zh-cn) - 针对运行带标记的 AOSP 版本分支的设备
	* [OTA 映像](https://developers.google.com/android/nexus/ota?hl=zh-cn) - 用于通过无线方式手动更新 Nexus 设备

2. 搭建编译环境

	Google官方构建编译环境指南中已经说明了Ubuntu14.04,Ubuntu 12.04,Ubuntu 10.04需要添加的依赖.

	下面是Ubuntu16.04中的依赖设置:

        sudo apt-get install libx11-dev:i386 libreadline6-dev:i386 libgl1-mesa-dev g++-multilib 
        sudo apt-get install -y git flex bison gperf build-essential libncurses5-dev:i386 
        sudo apt-get install tofrodos python-markdown libxml2-utils xsltproc zlib1g-dev:i386 
        sudo apt-get install dpkg-dev libsdl1.2-dev libesd0-dev
        sudo apt-get install git-core gnupg flex bison gperf build-essential  
        sudo apt-get install zip curl zlib1g-dev gcc-multilib g++-multilib 
        sudo apt-get install libc6-dev-i386 
        sudo apt-get install lib32ncurses5-dev x11proto-core-dev libx11-dev 
        sudo apt-get install libgl1-mesa-dev libxml2-utils xsltproc unzip m4
        sudo apt-get install lib32z-dev ccache
        
3. 准备构建
	
    1. 设置环境
    	
        使用envsetup.sh脚本初始化环境。
        
        	source build/envsetup.sh
            # 或者
            . build/envsetup.sh
            
    2. 选择目标
	
		使用lunch选择要编译的目标。确切的配置可作为参数进行传递。
        
        	lunch aosp_arm-eng
        
        该命令表示针对模拟器进行完整编译，并且所有调试功能均处于启用状态。

		如果您没有提供任何参数就运行命令，lunch 将提示您从菜单中选择一个目标。

		所有编译目标都采用 BUILD-BUILDTYPE 形式，其中 BUILD 是表示特定功能组合的代号。
        	
	3. 编译代码
	
		您可以使用 make 编译任何代码。GNU Make 可以借助 -jN 参数处理并行任务，通常使用的任务数 N 介于编译时所用计算机上硬件线程数的 1-2 倍之间。例如，在一台双核 E5520 计算机（2 个 CPU，每个 CPU 4 个内核，每个内核 2 个线程）上，要实现最快的编译速度，可以使用介于 make -j16 到 make -j32 之间的命令。
			make -j8

	4. 运行模拟器

		在编译完成之后，就可以通过以下的命令运行Android虚拟机了。
        
        	source build/envsetup.sh
            lunch (刚才的编译目标版本)
            emulator
            
  ## 小结
  到此为止，整个编译流程大致结束了。但是还有更多的Android底层知识等待着你。
  
  TODO LIST
  - 模拟器组成和原理
  - 模块编译
  - sdk编译
  - 错误记录
  
  
  ## 参考
  [自己动手编译Android源码(超详细)](http://www.jianshu.com/p/367f0886e62b) 作者-[@涅槃1992](http://www.jianshu.com/u/22f55ea83811)