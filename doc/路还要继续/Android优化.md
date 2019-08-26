---
	title : Android Eink应用优化指南 
	date : 2019年05月08日11:08:22
---

### Eink优化
Eink优化是指针对Eink设备刷新慢进行的UI优化

1. 避免滑动 
	
    自定义滑动控件。NoScrollViewPager在setcurrentitem时smoothscroll=false表示立即切换，不需要切换时间,PagerScrollView拦截touch事件，自己scrollto过去,PagerListView实现分页显示
2. 消除阴影 

	自定义弹窗控件。
    BottomDialog设置窗体不透明dimamount=0，设置window为全屏，设置window不获取焦点。
    DictionaryPopupWindow设置不获取焦点
3. 设置刷新模式

### 布局优化

如何检测：

1. 使用Hirearch View检测布局是否过于复杂
2. 使用TraceView检测CPU使用情况
3. 使用开发者选项中的show gpu overdraw查看是否存在过度绘制

如何优化：

1. 尽量减少布局层次，合理使用include,merge,ViewStub
2. onDraw()中不要大量创建临时对象
3. 如果是listview复用convertview，创建viewholder
 尽量使用性能高的控件，例如recyclerview

### 内存优化

如何检测：

1. 使用leakcanary检测
2. 使用mat工具分析java堆
3. 使用Android monitor查看memery

如何优化:

1. 主动释放内存，在onlowmemery()和onTrimMemery()中适当释放内存
2. 避免内存泄漏和内存溢出
3. 在使用bitmap的时候尽量先对bitmap进行压缩
4. 使用轻量级的数据结构，比如arraymap和sparsearry代替hashmap

5. 减少帧动画的使用,如果需要,通过SurfaceView实现
6. 合理的使用相关组件,比如Service和Webview,在不需要的时候主动结束其生命周期
7. 合理的使用多进程,比如像音乐播放器类,可以分为主进程和播放进程
8. 使用异步队列时考虑有界队列
9. 如果你能明确知道HashMap的大小,那就再初始化时为其制定容量

### 性能优化

如何检测:

1. 使用Lint执行静态分析，Android studio的Analysis->inspect code
2. 在开发者选项中开启strict mode
3. 代码review

如何优化:

1. 任务并行化，对可能的任务尽可能使用并行操作，如尽量使用线程池而非直接使用线程
2. 如果要使用序列化数据，尽量使用parsable而非java提供的serializable
3. 选择合适的数据结构
4. 使用Android提供的arraymap、sparsemap代替hashmap
5. 使用对象池技术
6. 使用缓存技术
7. 使用jni，对计算量较大的逻辑将其写成so文件，如图片处理
