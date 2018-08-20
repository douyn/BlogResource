## UI Automator
根据google的文档，Espresso主要用于单个应用的ui测试。UI Automator主要用于多个应用的ui测试。

允许让您在用户流跨越其他应用程序或者系统UI时验证您的应用程序是否正常运行。比如，一个即时通信的app，它允许用户打开联系人并选择要发送消息的人，然后返回app，并发送消息。

UI Automator允许您与设备上的可见元素进行交互，无论哪个Activity的位于前台，它都可以使用方便的描述符来查找UI组件。

### 使用UI Automator

#### 设置依赖
需要在build.gradle中配置依赖

    dependencies {
        ...
        androidTestImplementation 'com.android.support.test.uiautomator:uiautomator-v18:2.1.3'
    }
    
#### 检测应用程序的UI组件
Android sdk自带的uiautomatorview工具提供了方便的可视界面，用于检查布局层次并查看当前可见的ui属性。这些信息允许您使用UI Automator创建更细微的测试。

启动uiautomatorview工具：
1. 打开目标应用程序
2. 将设备连接到计算机
3. 打开终端窗口并进入<android-sdk>/tools/目录
4. 执行以下命令
	
    	$ uiautomatorviewer
        
查看ui属性：
1. 在uiautomatorviewer界面，点击"Device Screenshot"按钮
2. 将鼠标悬停在左侧面板的快照上即可查看该uiautomatorviewer工具识别的UI组件。属性位于右下方面板，布局层次结构位于右上方面板。
3. 在右上方布局层次结构上方，点击"Toogle NAF nodes"按钮可以查看UI AutomatorViewer无法识别的ui组件。
 
#### 确保Activity可以访问
UI Automator测试在已经实现了Android辅助功能的应用程序上表现更好。

当使用sdk中包含的view类或这view的子类的时候不用再添加辅助功能，因为这些类已经包含了这些工作。

但是如果要使用自定义的view的时候，这些元素不会提供可访问性支持，所以你要通过以下的步骤添加Android辅助功能。
1. 创建一个类extends ExploreByTouchHelper.
2. 通过调用将新类的实例与自定义view关联。

#### 创建UI Automator测试类
UI Automator测试类和junit 4的测试类的方法一样。

主要是需要在@Test中实现一下编程模型：

1. 获取到UiDevice对象，通过调用getInstance()并且需要传递Instrumentation对象作为参数
2. 通过findObject()方法获取当前设备显示的UiObject对象去访问ui组件
3. 通过调用UIObject的方法来执行交互，例如模拟多点触控手势，以及编辑文本字段。
4. 查看是否达到了预期的状态和行为

#### 指定选择器
如果要访问应用程序中特定的UI组件，请使用UiSelector类。

如果找到了多个匹配元素，则返回匹配元素的第一个元素作为UiObject。构建是可以使用链式编程以优化搜索。如果没有找到匹配元素会抛出异常。

可以使用childSelector去匹配子view

#### 执行UI操作
一旦你获取到UiObject对象，你就可以对该对象执行UI操作。
- click()
- dragTo()
- setText()
- swipeUp()
- swipeRight()

		
      Context context = getInstrumentation().getTargetContext();
            Intent intent = context.getPackageManager().getLaunchIntentForPackage("com.xxx");
            intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
            context.startActivity(intent);

            device.wait(Until.hasObject(By.pkg("com.xxx").depth(0)), 2000);
            
#### 对viewgroup进行操作
可以使用UiCollection去获取viewgroup。

#### 对可滑动view进行操作
可以使用UiScrollable类去获取可滑动views

#### 验证结果
你可以使用JUnit的Assert方法测试ui组件的结果是否是预期的结果。

#### run
与JUnit测试运行方法相同