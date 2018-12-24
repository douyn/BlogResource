## 阅读Android源码
由浅入深，同时横向和纵向阅读

#### 基础
Handler-Message-Lopper

Activity-Service

Fragment

View

MotionEvent

LayoutInflator

SurfaceView-TextureView

AsyncTask

Volley

android.util.*

#### 进阶
Context

ClassLoader

Binder

WMS-AMS-PMS-NMS-IMS

#### 第三方开源项目
EventBus

RxJava

Guava

再讲一下老罗给出的Android学习路线[那两年炼就的Android内功修养-(罗升阳)](http://blog.csdn.net/luoshengyang/article/details/8923485)，他给出三纵三横的路线，![](http://img.blog.csdn.net/20130528234751506)
1. 准备 (横向)

	- 阅读相关的书籍了解linux内核和Android应用基础知识
	- 搭建Android源码环境
	- C/C++基础，[Android系统的智能指针（轻量级指针、强指针和弱指针）的实现原理分析](http://blog.csdn.net/luoshengyang/article/details/6786239)
2. dalvik虚拟机 (横向)

	 Android系统的应用程序及部分应用程序框架是使用Java语言开发的，它们运行在Dalvik虚拟机之上，还有另外一部分应用唾弃框架在使用C/C++语言开发的。使用Java语言开发的应用程序框架老罗称之为Java Runtime Framework，而使用C/C++语言开发的应用程序框架老罗称之为C/C++ Runtime Framework，它们被Dalvik虚拟机一分为二。通过前面的学习，其实我们都已经了解Android系统的Java Runtime Framework和C/C++ Runtime Framework，因此，我们最后将注意力集中在Dalvik虚拟机上.
     [Dalvik虚拟机简要介绍和学习计划](http://blog.csdn.net/luoshengyang/article/details/8852432)
3. 专用驱动 (横向)，这些专用驱动就是指Logger,Binder和Ashmem，他们是整个Android系统的基石。

	- Logger，[Android日志系统驱动程序Logger源代码分析](http://blog.csdn.net/luoshengyang/article/details/6595744)
	- Binder, [Android进程间通信（IPC）机制Binder简要介绍和学习计划](http://blog.csdn.net/luoshengyang/article/details/6618363)
	- Ashmem, [Android系统匿名共享内存Ashmem（Anonymous Shared Memory）简要介绍和学习计划](http://blog.csdn.net/luoshengyang/article/details/6651971)
4. HAL (纵向)
	硬件抽象层最适合用作Android系统的学习入口，它从下到上涉及到了Android系统的各个层次。
    - [Android硬件抽象层（HAL）概要介绍和学习计划](http://blog.csdn.net/luoshengyang/article/details/6567257)
    - [在Ubuntu上为Android系统的Application Frameworks层增加硬件访问服务](http://blog.csdn.net/luoshengyang/article/details/6578352)
5. Application Component (纵向)

	应用程序组件是Android系统的核心，为开发者提供了贴心服务，应用程序组件有四种，围绕应用程序组件，又有应用程序进程、消息循环和安装三个相关模块。
    - Activity
    - Service
    - BroadcastReceiver
    - ContentProvider
    - 进程
    - 消息循环
    - 安装
6. ui (纵向)

	6.1 ui框架
    
	![4.2ui渲染图](http://img.blog.csdn.net/20130529004204159)
    
    上图可以看出关于Android的UI架构的三条主线：
    - 每一个window的surface是怎么渲染的
    - surfaceflinger是怎样合成每一个window的surface
    - windowmanagerservice是怎样管理window的

	6.2 分析路线

	6.2.1 首先以开机动画作为切入点，了解linux内核中的驱动。
    
    FB驱动抽象了显卡，上面的用户控件程序就是通过它显示UI的
    
    HAL层的Gralloc模块对FB驱动进行了封装，以方便SurfaceFlinger对他进行访问
    [Android帧缓冲区（Frame Buffer）硬件抽象层（HAL）模块Gralloc的实现原理分析](http://blog.csdn.net/luoshengyang/article/details/7747932)
    
    SurfaceFlinger负责合成各个应用程序窗口的ui，也就是将各个窗口的ui合成，并通过FB显示在屏幕上
    [Android应用程序与SurfaceFlinger服务的关系概述和学习计划](http://blog.csdn.net/luoshengyang/article/details/7846923)
    
    6.2.2 分析SurfaceBlinger
    
    [Android系统Surface机制的SurfaceFlinger服务简要介绍和学习计划](http://blog.csdn.net/luoshengyang/article/details/8010977)
    
    6.2.3 SurfaceFlinger的操作对象是应用程序窗口，因此我们要掌握应用程序窗口的组成
    
    [Android应用程序窗口（Activity）实现框架简要介绍和学习计划](http://blog.csdn.net/luoshengyang/article/details/8170307)
    
    6.2.4 应用程序窗口是windowmanagerservice管理的，并且也是wms负责提供窗口信息给surfaceflinger的，因此需要分析wms
    
    [Android窗口管理服务WindowManagerService的简要介绍和学习计划](http://blog.csdn.net/luoshengyang/article/details/8462738)
    
    6.2.5 以上Android系统的ui框架就清晰了，但是前面所研究的应用程序的窗口还是太抽象了，再去具体研究一下某些系统ui控件是怎么实现的。例如：
    
    [Android控件TextView的实现原理分析](http://blog.csdn.net/luoshengyang/article/details/8636153)
    
    [Android视图SurfaceView的实现原理分析](http://blog.csdn.net/luoshengyang/article/details/8661317)
    
    6.2.6 最后分析一下资源管理框架，他有效分离了代码和ui
    
	[ Android资源管理框架（Asset Manager）简要介绍和学习计划](http://blog.csdn.net/luoshengyang/article/details/8738877)

	[Android应用程序资源的编译和打包过程分析](http://blog.csdn.net/luoshengyang/article/details/8744683)
    