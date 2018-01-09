## Android Service
![service知识点](http://upload-images.jianshu.io/upload_images/3513995-0246e53ee61b8802.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###### 什么是service？
service是一个长时间运行在后台但不提供用户界面的应用组件。当切换到其它应用，Service仍能在后台继续运行。

###### Service两种启动方式
###### 1. bindservice
通过bindservice方式启动的服务是跟启动他的组件绑定在一起的。如果多个组件绑定了一个服务，当所有组件都销毁时，服务才停止，如果只有一个组件绑定了服务，这个组件销毁，服务就停止。
onCreate()-->onBind()-->onUnBind()-->onDestroy()

服务是如何被绑定的？
1. 首先添加一个类，继承Binder，在Binder类中添加与其它组件要与服务交互的方法，并在onBind()方法中返回IBinder实例。
2. 在创建服务的时候需要传递ServiceConnection对象。在接口回调中获取binder对象与服务通信。
3. 绑定和解绑服务。
###### 2. startservice
通过startservice方式启动的服务，一旦启动就会独立于启动他的组件，即使组件销毁了也会继续在后台执行。
onCreate()-->onCommandStart()-->onDestroy()

###### 3. startservice并且bindservice
当一个service在startservice的同时bindservice，该Service即使所有的与之绑定的组件都销毁了这个组件也不会停止，如果你想要手动去结束掉服务，必须调用stopService或者stopself方法。

###### 组件和服务之间进行通信
1. BroadcastReceiver

	如果是以startservice方式启动服务，那么他与组件将是相对独立的。

2. BindService

	组件和服务之间通信的常用方式

3. AIDL

#### IntentService
>IntentService is a base class for {@link Service}s that handle asynchronous 
requests (expressed as {@link Intent}s) on demand. Clients send requests 
through {@link android.content.Context#startService(Intent)} calls; the 
service is started as needed, handles each Intent in turn using a worker 
thread, and stops itself when it runs out of work.

IntentService是一个异步处理的类，你可以通过startservice来提交请求，该Service会在需要的时候创建，请求是在工作线程处理，并且Service会在执行完销毁。

所以IntentService是Service的子类，他是使用工作线程逐一处理启动请求，如果你不要求服务同时处理多个请求，这是最好的选择。

分析IntentService源码：
oncreate(): 创建一个工作线程，获取到这个工作线程的looper对象用于意图处理，创建一个ServiceHandler对象用于处理消息。
onStart()/onCOmmandStart():发送消息给handler
ServiceHandler.handleMessage()中会回调onHandleIntent(),之后调用stopSelf()方法停止service

#### 总结
###### Service
Service 有2种启动方式，startService 启动服务，服务启动起来后，在后台无限期运行，直到通过stopService 或者 stopSelf 停止服务，服务与组件独立，通信比较困难（但还是有办法的，通过BroadcastReceiver ）。另一种方式就是 bindService 即绑定服务，组件和服务绑定在一起，服务的生命后期受组件影响，如果绑定到服务的组件全部被销毁了，那么服务也就会停止了。绑定服务的方式通常用于组件和服务之间 需要相互通信。startService 这种 方式一般用于在后台执行任务，而不需要返回结果给组件。 这两种方式并非完全独立，也就是说，你可以绑定已经通过 startService 启动起来的服务，可以通过在Intent 中添加Action 来标示要执行的动作。比如：通过Intent Action 标记要播放的音乐，调用startService 来启动音乐服务播放音乐，在界面需要显示播放进度的时候，可以通过binderService 来绑定服务，从而获取歌曲信息。这种情况下，Service 需要实现两种方式的生命周期。这种情况下，除非所有客户端都已经取消绑定，否则通过stopService 或者 stopSelf 是不能停止服务的。

Service 是运行在主线程中的，因此不能执行耗时的或者密集型的任务，如果要执行耗时操作或者密集型计算任务，请在服务中开启工作线程，在线程中执行。或者使用下面一节将要讲的IntentService。

###### Intentservice
IntentService是Service 的子类，默认给我们开启了一个工作线程执行耗时任务，并且执行完任务后自 动停止服务。扩展IntentService比较简单，提供一个构造方法和实现onHandleIntent 方法就可了，不用重写父类的其他方法。但是如果要绑定服务的话，还是要重写onBind 返回一个IBinder 的。使用Service 可以同时执行多个请求，而使用IntentService 只能同时执行一个请求。




#### 参考
[Service和IntentService详解](http://blog.csdn.net/qq_23205911/article/details/73430954)

