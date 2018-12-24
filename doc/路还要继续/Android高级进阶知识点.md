#### 1. 触摸事件传递机制
1. 触摸事件的类型（MotionEvent(ACTION_DOWN,ACTION_MOVE,ACTION_UP等)）
2. 触摸事件的三个阶段（分发-拦截-消费）
3. view的时间传递机制（activity->view）
4. viewgroup的时间传递机制 (activity->viewgroup->view)

#### 2. Android View的绘制流程
[setContentView方法详解](http://blog.csdn.net/yanbober/article/details/45970721)

1. Android UI管理系统的层级关系(activity-phonewindow-decorview-(titleview,contentview))
2. 绘制的整个流程(performtraversals->performMeasure->performLayout->performDraw)
3. MesureSpec(unspecified,at_most(wrap_content),exactly(match_parent))
4. Measure
5. layout
6. draw(drawBackground-saveLayer-onDraw-dispatchDraw-restoreToCount-onDrawScrollBar)

#### 3. Android动画
1. 帧动画（xml方式/动画方式添加）
2. 补间动画（alpha/rotate/scale/translate，添加方式）
3. 属性动画（Evaluator，animatorset，valueanimator，objectanimator）
4. 过渡动画（transition）（本质是属性动画，但是对属性动画做了封装，方便实现过度动画的效果。需要动画前后两个布局）（定义transition的步骤：1. 定义动画前后两个布局 2. 根据布局文件创建布局前后的scene 3. 创建过度动画transitionset 4. 调用TransitionManager.go(scene, transitionSet)）

	- scene（定义页面信息）
	- transition（changebounds，fade，transitionset，autotransition）
	- transitionmanager（切换控制器）

#### 4. Support Annotation Library
1. 基础概念（support library，support annotion library）
2. Nullness注解（@NonNull，@Nullable）
3. 资源文件类型注解（@AnimatorRes,@AnimRes,@AnyRes,@ArrayRes,@AttrRes,@BoolRes,@ColorRes,@DrawableRes,@FractionRes,@IdRes,@IntegerRes,@InterpolatorRes,@LayoutRes,@MenuRes,@PluralsRes,@RawRes,@StringRes,@StyleableRes,@StyleRes,@TransitionRes,@XmlRes）
4. 权限注解(@RequiresPermission)
5. 类型定义注解(@IntDef @interface @Retention，定义注解类)
6. 线程注解（@MainThread，@UiThread, @WorkThread,@BinderThread）
7. 颜色值注解(@ColorInt)
8. 值范围注解(@Size(n),@Size(max=m),@Size(min=n),@Size(Multiple=2),@IntRange(from=0,to=255),@FloatRange(from=0.0,to=1.0))
9. 重写函数注解(@CallSuper)
10. 返回值注解(@CheckResult)
11. keep注解(@keep)
12. visibletest注解(@VisibleTest)
13. lint的用法(Analyze->Inspect Code->选择范围)

#### 5. Percent Support Library（百分比布局）
1. PercentRelativeLayout
2. PercentFramelayout
3. layout_heightPercent/layout_widthPercent
4. aspectRatio

#### 6. Design Support Library
1. 简介，snackbar，navigationview,floatingactionbutton,coordinatorlayout,constraintlayout,collapsingtoolbarlayout
2. snackbar（setAction）
3. textinputlayout(用法设置为edittext的父布局)
4. tablayout
5. navigationview
	
    5.1 导航菜单
    5.2 导航头部

6. FloatingActionButton

	6.1 基本属性（elevation（未按压状态的阴影），pressedTranslationZ（按压状态的阴影））
	6.2 其它选项（backgroundTint（背景颜色），rippleColor（波纹颜色），fabSize（可选尺寸））

7. CoordinatorLayout(协调布局，可以使不同布局的组件之间直接相互作用，并且协调动画效果)（例如，floatingactionbar和snackbar，点击floatingactionbutton出现snackbar，然后floatingactionbutton自动往上移动，snackbar消失时，floatingactionbutton自动往下移动，这里就需要将coordinatorlayout作为floatingactionbutton的父容器）
8. CollapsingToolbarLayout(可以实现当屏幕内容滚动时，toolbar收缩的效果，通常和appbar配合使用)
9. BottomSheetBehavior（1. 布局中添加app:layout_behavior属性，并将这个view作为coordinatorlayout的子view 2. 布局文件中创建bottomsheetbehavior对象，并设置状态变化的监听）

#### 7. NDK开发
1. ABI（应用程序二进制接口application binary interface）（arm-v5,arm-v7,arm-v8,x86,x86_64,mips,mips6）
2. 引入预编译的二进制文件
3. 自己写c/c++文件
4. 使用.so文件的注意事项

#### 8. Gradle必知必会
1. 概念（是Android studio标配的构建系统，settings.gradle,project build.gradle,module build.gradle,gradle的文件结构）
2. 共享变量（1. 在项目路径下创建xxx.gradle文件 2. 在文件中定义变量如project.ext{javaVersion = 1.8} 3. 在module build.gradle中apply from: "${project.rootDir}/xxx.gradle"，然后引用变量名就可以）
3. 通用配置（在project build.gradle下创建subprojects{}定义配置）
4. 如何引用aar（1. 指定aar文件的位置，在module build.gradle中的android节点下创建repositories{flatDir{dirs 'libs'}} 2. 在dependencies节点下添加compile(name: 'xxx', ext: 'aar')）	
5. 签名混淆的配置

#### 9. gradle打包发布
1. maven central
2. jcenter
3. jitpack

#### 10. Builder建造者模式详解
1. 经典的建造者模式（java设计模式之建造者模式）
2. 建造者模式的变种
3. 变种的自动化生成
4. 使用builder模式的开源例子

#### 11. 注解在Android中的使用
1. 注解的定义（java语言的特性之一，他是在源代码中插入的标签，在之后的编译和运行中起到某种作用，每个注解都必须通过@interface进行声明，接口的方法对应着注解的元素）
2. 标准注解（java包中默认给出的注解）
	
    2.1 编译相关的注解（@override，@deprecated，@suppresswarnings，@safevarargs，@generated，@functuinalinterface）
    
    2.2 资源相关的注解（javaee中用到）（@PostConstruct，@PreDestroy，@Resource，@resources）
    
    2.3 元注解（用来定义和实现注解的注解）
	
		2.3.1 @Target（指定注解所适用的对象的范围）
		
        2.3.2 @Retention（用来指明注解的访问范围，也就是在什么级别保留注解）（源码级注解（@Retention(RetentionPolicy.SOURCE)）,编译级注解(@Retention(RetentionPolicy.CLASS)),运行时注解（@Retention（RetentionPolicy.RUNTIME）））

		2.3.3 @Documented（表示被修饰的注解应该保存在被注解项的文档中）

		2.3.4 @Inherited（表示该注解可以被子类继承）

		2.3.5 @Repeatable（表示这个注解可以在注解项上应用多次）

3. 运行时注解（该注解会被保存在.class和.java文件中，在执行时，也保留注解信息。可以通过反射机制读取注解的信息）
4. 编译时注解

#### 12. ANR产生的原因及定位分析
1. ANR产生的原因

	只有当UI线程响应超时才会引起ANR，主要两种原因导致超时，1. 事件被阻塞，正在处理其它阻塞事件 2. 自身处理事件耗时太长。
    activity/view 5s， broadcast 10s， Service 20s
2. 经典场景

	1. 应用程序UI线程中存在耗时操作，例如在UI线程中进行网络操作，数据库操作和文件操作等，导致ui线程阻塞无法响应用户数据等。
	2. 应用程序的ui线程在等待工作线程释放某个锁，从而无法处理事件
	3. 耗时的动画需要大量的计算工作，可能导致CPU负荷过重
3. 定位和分析

	1. logcat分析可以看到是anr发生的进程及包名和类名，pid，reason，cpu使用情况
	2. 如果还是没有具体定位到anr发生的位置，就需要借助/data/anr/xxx.txt文件。文件包括发生anr的进程名，id和时间，cpu架构，堆内存的使用情况，线程信息，以及anr发生的位置及错误信息。
4. 避免和检测
	不要在主线程做耗时操作，使用检测工具检测存在违规操作的工具类

#### 13. Android异步处理技术
常见的异步处理技术：Thread，AsyncTask，Handler & Looper， Executors。

Handler异步处理消息机制：

1. Looper.prepare方法会在本线程中创建一个Looper实例，在实例化过程中会创建一个MessageQueue，这个looper实例会被保存到ThreadLocal对象中(ThreadLocal对像可以再线程中存储变量)
2. Looper.loop方法会让当前线程进入一个无限循环，不断的从Messagequeue中得到message对象，并且通过msg.target.dispatchMessage将消息发送出去
3. Handler的构造方法会获取当前线程中保存的looper实例，并与looper对象的messagequeue关联起来
4. handler.sendmessage中将msg.tar设置为handler对象本身，并将message添加到messagequue中
5. msg.target.dispatchmessage即是handler.dispatchmessage，他会回调handler.message方法

###### 1. Thread
概念： 执行任务的基本单元

2种创建方式： 继承Thread，实现runnable

3种线程的分类：

- 主线程（ui线程），随着应用启动而启动，用来运行Android组件，刷新组件元素。通过handle顺序处理其它线程发来的消息。
- binder线程，Binder线程用于不同进程之间的通信，每个应用在创建之后，就创建了一个线程池，用于处理其它进程的线程发送的消息，经典场景是aidl
- 后台线程（工作线程）

###### 2. HandlerThread
HandlerTread是Thread的子类，它内部集成了Looper和MessageQueue。内部其实也是handler机制。

###### 3. AsyncQueryHandler
主要用于contentprovider中的异步数据查询，不常用
###### 4. AsyncTask
    asynctask task = new asynctask(){
        onpreexecute();
        onpostexecute();
        doinbackground();
        onprogressupdate();
    };
    task.execute();
    
构造方法中创建mHandler,mWork,mFuture->execute()->executeOnExecutor() 1. onpreexecute() 2. exec.execute()->exec.execute() exec对象是在execute方法中的参数，是sdefaultexecutor成员变量实际上是一个SerialExecutor类的实例->创建serialexecutor对象会首先生成arraydeque队列，将mfuture对象封装成runnable添加到队列中，判断mactive是否为空，如果为空执行scheduleNext()->这个方法会先去将队列的队尾赋值为mactive，然后执行THREAD_POOL_EXECUTOR.execute(mactive)方法->实际上THREAD_POOL_EXECUTOR是一个线程池，相当于执行线程的run方法->mactive对象实际上是mFuture，FutureTask.run实际上会执行callable.call()->mWork.call() 1. 执行doinbackground 2. 执行postresult->postresult 1. 封装result为message，message 2. 调用message.senttotarget发送。实际上是调用handle处理消息->mhandler对象handlemessage,两种事件 1. PostResult 回调onCancel,onPostExecute 2. PostProgress 回调onProgressUpdate

###### 5. Loader
不常用
#### 14. Android数据序列化
#### 15. WebView



	
   