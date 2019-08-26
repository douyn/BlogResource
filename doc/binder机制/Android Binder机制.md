## Android Binder机制
#### 概述
Android系统中每个应用程序四大组件之间的通信，底层都是依赖binder机制。不仅是应用层，在整个Android架构中，大量使用binder作为IPC通信的机制。

#### 原理
##### IPC的原理
![](http://gityuan.com/images/binder/prepare/binder_interprocess_communication.png)
从进程的角度看，每个应用程序都只能运行在自己的进程所在的虚拟地址空间，对于一个4g的虚拟地址空间，其中3g是用户空间，1g是内核空间，对于用户空间，不同的进程间不能彼此访问，而内核空间是可以共享的，client和service之间的通信，恰恰是利用进程间可共享内存来实现的。

##### Binder的原理
Binder通信采用C/S架构，由client，service，service manager和binder驱动程序组成。
![](http://gityuan.com/images/binder/prepare/IPC-Binder.jpg)
其中，client和service分别对应c/s，service manager用于管理系统中的各项服务，binder驱动程序是其它各组件交互的中间驱动。

(1) client，service，service manager运行在用户空间，binder驱动运行在内核空间。

(2) service manager和binder驱动是Android系统定义好的，开发者只要定义好client和service即可进行进程间通信。

(3) binder驱动程序提供设备文件/dev/binder与用户空间交互，client，service，service manager通过ioctl等方法和内核空间进行交互。

(4) client和service交互的步骤为Service首先到Service manager上注册服务，client通过Service manager获取服务，client使用服务。

#### c/s模式
BpBinder和BBinder都是Android中Binder通信相关的代表，他们都是从IBinder类中派生而来。
![](http://gityuan.com/images/binder/prepare/Ibinder_classes.jpg)

- client: BpBinder.transact()发来事物请求。
- service: BBinder.ontransact()接收事物请求。

#### 源码目录(android-4.4.2 全志-b288)
- java 

		./android/framework/base/core/java
- jni 

		./android/framework/base/core/jni
- native 

		./android/framework/native/lib/binder/
        ./android/framework/native/cmds/servicemanager/
- driver 
		
        ./lichee/linux-3.10/drivers/staging/android/

###### java framework
	./android/framework/base/core/java/android/os
    	- IInterface.java
        - IBinder.java
        - Parcel.java
        - IServiceManager.java
        - ServiceManager.java
        - ServiceManagerNative.java
        - Binder.java

	./android/framework/base/core/jni/
    	- android_os_Parcel.cpp
    	- AndroidRuntime.cpp
    	- android_util_Binder.cpp(核心类)
###### native framework
	./android/framework/native/libs/binder/
    	- IServiceManager.cpp
    	- BpBinder.cpp
    	- Binder.cpp
    	- IPCThreadState.cpp(核心类)
    	- ProcessState.cpp(核心类)
    
    ./android/framework/native/include/binder/
    	- IServiceManager.h
    	- IInterface.h

	./android/framework/native/cmds/servicemanager/
    	- service_manager.c
    	- binder.c
###### kernel
	./lichee/drivers/staging/android/
    	- binder.c
    	- uapi/binder.h
       
#### 参考
[Binder系列-gityuan](http://gityuan.com/2015/10/31/binder-prepare/)

[Android - Binder驱动-_doing](https://www.cnblogs.com/Doing-what-I-love/p/5530173.html)
