## Espresso UI自动化测试框架
#### 前言
Espresso是谷歌大力推荐的一套测试框架，从Android studio2.2版本开始，google就开始支持在as上espresso自动生成单元测试代码。

Espresso测试运行速度很快，它允许你在应用程序处于静止时操作和断言等。

Espresso面向那些认为自动化测试是开发声明周期的一部分的开发人员，并且虽然它可以被用作黑盒测试，但是熟悉代码库的人可以解锁Espresso的全部功能。

#### 简单实例
Espresso非常简单医用，并且可定制，能清楚的表明期望，交互和断言。

    @Test
    public void test(){
		onView(withId(R.id.test)).perform(click()).check(matches(withText("clicked")));
    }
    
每次调用onView()时，Espresso都会等待执行相应的操作和断言，知道满足一下条件为止。
- The message queue is empty.
- There are no instances of AsyncTask currently executing a task.
- All developer-defined idling resources are idle.

#### 依赖的类库
- espresso-core 包含基础的视图匹配器，操作和断言库
- espresso-web 包含webview的测试库
- espresso-idling-resource 包含和后台作业的同步的机制
- espresso-contrib 包括DatePicker, RecyclerView and Drawer actions, accessibility checks, and CountingIdlingResource这些的扩展库
- espresso-intent 包括与意图有关的测试库
- espresso-remote Espresso多处理功能的位置

#### 设置和使用
##### 1. 设置测试环境
为了避免测试过程出现问题，最好关闭开发者选项中的下边三个设置。

- Window animation scale
- Transition animation scale
- Animator duration scale

##### 2. 配置gradle

	dependencies{
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test:rules:1.0.2'
    }
    ...
    android{
    	defaultConfig{
        	testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
        }
    }

> 注意： Android Studio现在新建项目一般都会依赖部分Espresso的库，但是有的仍需要引入部分库，比如我的Android studio 3.0.1需要引入'com.android.support.test:rules:1.0.2'库

##### 3. 编写测试代码
	@LarggeTest
	@RunWith(AndroidJUnit4.class)
    public class MainActivityTest{
    	@Rule
        public ActivityTestRule mActivityRule = new ActivityTestRule(MainActivity.class);
        
        @Test
        public void runTest(){
        	onView(withId(R.id.test)).perform(click()).check(matches(withText("2")));
        }
    }
    
> 注意： ActivityTestRule需要导入上一步的一个rules库。
> 如果onView等方法没有代码提示，可以手动先导入相关类库。
    
##### 4. 运行测试用例
可以使用as的运行功能也可以使用命令行方式

###### 在Android Studio中：
1. 打开Run -> Edit Configurations
2. 添加一个新的测试配置
3. 选择module
4. 添加一个指定的runner：android.support.test.runner.AndroidJUnitRunner
5. 运行新配置

###### 在命令行中
执行以下代码：

	./gradlew connectedAndroidTest

#### Espresso API
Espresso API鼓励开发者根据用户与应用程序交互时可能执行的操作进行思考-定位UI元素并与之进行交互，同时框架阻止直接访问应用程序的活动和视图。

API中主要包括以下的包：
- Espresso 与视图交互的入口(onView(),onData()),同时公开不一定与视图绑定的api，例如pressBack()
- ViewMatchers 一组实现了Matcher< ? super View>接口的对象，你可以将这个作为筛选条件在onView中定位视图
- ViewAction 一组可以被传递给ViewInteraction.perform()的ViewAction对象，比如click()
- ViewAssertions 一组可以被传递给ViewInteraction.check()方法的ViewAssertion对象，大多数情况下，你需要使用这个匹配断言，当一个viewmatcher去断言一个view的状态的时候。

实例：

	onView(withId(R.id.test)) // withId() is a ViewMatcher
        .perform(click()) // click() is a ViewAction
        .check(matches(isDisplayed())); // matches(isDisplayed()) // is a ViewAssertion, isDisplayed() is a ViewMatcher
        
        
###### 定位视图
通常期望的视图具有唯一性，但是有的情况下，通过ViewMater匹配的视图不唯一，此时就会报出异常：

    android.support.test.espresso.AmbiguousViewMatcherException
    此匹配器匹配层次结构中的多个视图:( withId：<123456789>）
    
所以Espresso提供了组合匹配器来缩小搜索范围：
	
    onView(allOf(withId(R.id.my_view), withText("Hello!")))

也可以使用反转匹配器
	
    onView(allOf(withId(R.id.test), not(withText("Hello"))));
    
> 注意：
> 1. 在行为良好的应用程序中，可交互视图都有描述性文本和内容描述文本。如果您无法使用withText()或 缩小搜索范围withContentDescription()，请考虑将其视为可访问性错误 
> 2. 通常，一个视图都有唯一的标示。当一个视图有唯一的标示时，就不需要再去指定其他的匹配器去匹配。
> 3. 如果一个目标view是adapterview，例如listview，gridview或者spinner，请使用onData()去获取数据

请参阅[ViewMatchers](https://developer.android.google.cn/reference/android/support/test/espresso/matcher/ViewMatchers.html) Espresso提供的视图匹配器。

###### 操作视图
当找到目标视图之后，可以使用perform方法对其进行操作.
例如要单击视图：

	onView(...).perform(click());
    
执行多个操作：

	onView(...).perform(typeText("hello"), click());
    
如果目标视图位于scrollview内部，需要考虑显示视图之前的操作，比如scrollTo().

	onView(...).perform(scrollTo(), click());

请参阅[ViewActions](https://developer.android.google.cn/reference/android/support/test/espresso/action/ViewActions.html)查看详细API操作。
###### 断言检查
可以使用check()将断言应用与当前视图。最常见的断言是matches()断言。

下边的示例中，SimpleActivity包含a Button和a TextView。单击按钮时，内容TextView将更改为"Hello Espresso!"。

	// 匹配视图button及操作视图
	onView(withId(R.id.button_simple)).perform(click());
    
    // 验证文本
    onView(withId(R.id.text_simple)).check(matches(withText("Hello Espresso!")));
    
###### 检查AdapterView中的数据加载
AdapterView比较特殊，它只能显示当前数据的一个子集的view，所以onView()不会匹配到那些没有显示的view。

Espresso单独提供了onData()入口来处理这个问题，该入口能够首先加载所有的item。

下边是一个使用 onData()的例子。

	// open the item selection
    onView(withId(R.id.spinner_simpke)).perform(click());
    // select an item
    onData(allOf(instanceOf(String.class)), is("Americano")).perform(click());
    // verify text correct
    onView(withId(R.id.spiinertext_simple)).check(matches(withText(containsString("Americano"))))
    
#### Espresso recipes
本篇主要介绍Espresso的进阶用法。

##### 匹配另一个视图旁边的视图
![匹配另一个视图旁边的视图](https://developer.android.google.cn/images/training/testing/repeated-view.png)
如上图所示，如果我们要去匹配7旁边的item视图时，因为7是非唯一视图，item是唯一视图，我们可以使用组合匹配器，先去匹配非唯一视图，然后使用hasSibling(0匹配器缩小选择范围。

	onView(allOf(withText(7), hasSibling(withText("item: 0")))).perform(click());
    
##### 匹配Actionbar里边的视图
主要是指 ActionBar的控件和Option Item
如果是在actionbar中可见的的视图，是比较简单的。
	
    onView(withId(R.id.xxx)).perform(click());
    
对于option item来说，有的是通过右上角的menu button调出的，有的是通过虚拟菜单键调出的。幸运的是，Espresso为我们处理了额这个问题。

对于正常的操作栏：

	openActionBarOverflowOrOptionsMenu(getInstrumentation().getTargetContext());
    onView(withText("World")).perform(click());
    
对于上下文相关的操作栏

	openContexturlActionModeOverflowMenu();
    onView(withText("Hello")).perform(click());
    
##### 断言view是否显示

	onView(withId(R.id.test)).check(matches(not(isDisplayed())));
##### 断言view是否存在

	onView(withId(R.id.test)).check(doesNotExist());
    
##### 断言数据项不在适配器中
断言数据想不在适配器中需要我们拿到adapter item，并与数据做匹配。这需要我们自己定义方法去匹配。

	private static Matcher<View> withAdapterData(Matcher<Object> dataMatcher) {
    	@Override
        public void describeTo(Description description){
            description.appendText("with class name: ");
            dataMatcher.describeTo(description);
        }
        
        @Override
        public boolean matchesSafely(View view) {
        	if (!(view instanceof AdapterView)) {
            	return false;
            }
            
            Adapter adapter = view.getAdapter();
            for (int i = 0; i < adapter.getCount(); i++) {
            if (dataMatch.matches(adapter.getItem(i))){
            	return true;
            }
            }
            
            return false;
        }
    }
	
	onView(withId(R.id.list)).check(matches(withAdapterData(withItemContent("item 1"))));
    
> 注意： 部分代码详见[AdapterViewTest.java](https://android.googlesource.com/platform/frameworks/testing/+/android-support-test/espresso/sample/src/androidTest/java/android/support/test/testapp/AdapterViewTest.java)

##### 自定义故障处理程序
espresso允许开发者自定义FailureHandler用于错误处理，常见的做法有收集错误信息，截图或者传递额外的调试信息。

    private static class CustomFailureHandler implements FailureHandler {
    	private final FailureHandler delegate;
		public CustomFailureHandler(Context targetContext) {
        	delegate = new DefaultFailureHandler(targetContext);
        }
        
        @Override
        public void handle(Throwable error, Matcher<View> viewMatcher){
        	try {
            	delegate.handle(error, viewMatcher);
            } catch (NoMatchingViewException e) {
            	throw new MySpecialException(e);
            }
        }
    }

需要自己定义Exception，并且在test中setFailureHandler();一般在setUp()中定义。

##### 定位非默认窗口

	onView(withText("south china sea"))
    	.inRoot(withDecorView(not(is(getActivity().getWindow().getDecorView()))))
        .perform(click());

##### 在列表视图中匹配页眉和页脚
想要匹配页眉和页脚，必须在listview.addFooter()或者listview.addHeader()中传递第二个参数。这个参数起到关联作用。例如：
	
    public static final String FOOTER = "FOOTER";
    ...
    View footerView = layoutInflater.inflate(R.layout.list_item, listView, false);
    ((TextView) footerView.findViewById(R.id.item_content)).setText("count:");
    ((TextView) footerView.findViewById(R.id.item_size)).setText(String.valueOf(data.size()));
    listView.addFooterView(footerView, FOOTER, true);
    
测试代码：

	public static Matcher<Object> isFooter(){
    	return allOf(is(instanceOf(String.class)), is(FOOTER));
    }

	public void testFooter(){
    	onData(isFooter()).perform(click());
    }
    
#### 多进程
espresso允许跨进程测试，但是只是在Android 8.0及以上版本。所以请注意以下两点：
- 应用最低版本为8.0，也就是API 26。
- 只能测试应用内的进程，无法测试外部进程。

##### 使用步骤
在build.gradle中引用espresso-remote库

	dependencies {
    ...
    androidTestImplementation 'com.android.support.test.espresso:espresso-remote:3.0.2'
	}
    
并且需要在androidTest的Manifest文件中添加以下代码：

	<instrumentation android:name="android.support.test.runner.AndroidJUnitRunner"
    android:targetPackage="android.support.mytestapp"
    android:targetProcesses="xxx"/>
	
    <meta-data
    android:name="remoteMethod"
    android:value="android.support.test.espresso.remote.EspressoRemote#remoteInit" />
    
#### 辅助功能检查
AccessibilityCheck允许使用现有代码来测试可访问性问题。它会在测试动作方法之前发生。

	import android.support.test.espresso.contrib.AccessibilityChecks;

    @RunWith(AndroidJUnit4.class)
    @LargeTest
    public class AccessibilityChecksIntegrationTest {
        @BeforeClass
        public static void enableAccessibilityChecks() {
            AccessibilityChecks.enable();
        }
    }
    
#### list
list分为两种adapterview和recyclerview。

##### 与adapterview列表项的交互
普通写法

	onData(allOf(is(instanceOf(Map.class)), hasEntry(equalTo("STR"), is("item: 50"))));

注意，Espresso会根据需要自动滚动列表。

我们分析上边的代码，首先is(instanceOf(Map.class))会将搜索范围缩小到map集合，然后hasEntry()会去匹配集合里key为“str”和value为"item: 50"的条目。

因为上述代码较长，我们可以自定义matcher去match

	private static Matcher<Object> withItemContent(String expectedText) {
    	checkNotNull(expectedText);
        return withItemContent(equalTo(expectedText));
    }
    
    private static Matcher<Object> withItemContent(Matcher<Object> itemTextMatcher) {
        return new BoundedMatcher<>(Map.class){
        	@Override
    public boolean matchesSafely(Map map) {
        return hasEntry(equalTo("STR"), itemTextMatcher).matches(map);
    }

    @Override
    public void describeTo(Description description) {
        description.appendText("with item content: ");
        itemTextMatcher.describeTo(description);
    }
        }
    }
    
这样就可以简单的调用了
	
    onData(withItemContent("xxx")).perform(click());
    
##### 操作child view

	onData(withItemContent("xxx")).onChildView(withId(R.id.tst)).perform(click());
    
    
#### 和recyclerview的条目交互
espresso-contrib库中包含一系列recyclerviewactions。
- scrollTo 滚动到匹配的view
- scrollToHolder 滚动到匹配的viewholder
- scrollToPosition 滚动到指定的position
- actionOnHolderItem 在匹配到的view holder中进行操作
- actionOnItem 在匹配到的item view上进行操作
- actionOnItemAtPosition 在指定位置的view上进行操作

实例：

    @Test
    public void scrollToItemBelowFold_checkItsText() {
        // First, scroll to the position that needs to be matched and click on it.
        onView(ViewMatchers.withId(R.id.recyclerView))
                .perform(RecyclerViewActions.actionOnItemAtPosition(ITEM_BELOW_THE_FOLD,
                click()));

        // Match the text in an item below the fold and check that it's displayed.
        String itemElementText = mActivityRule.getActivity().getResources()
                .getString(R.string.item_element_text)
                + String.valueOf(ITEM_BELOW_THE_FOLD);
        onView(withText(itemElementText)).check(matches(isDisplayed()));
    }
    @Test
    public void itemInMiddleOfList_hasSpecialText() {
        // First, scroll to the view holder using the isInTheMiddle() matcher.
        onView(ViewMatchers.withId(R.id.recyclerView))
                .perform(RecyclerViewActions.scrollToHolder(isInTheMiddle()));

        // Check that the item has the special text.
        String middleElementText =
                mActivityRule.getActivity().getResources()
                .getString(R.string.middle);
        onView(withText(middleElementText)).check(matches(isDisplayed()));
    }
    
#### 意图
Espresso-intents库是对espresso的扩展，可以对被测应用程序发出的意图进行验证和存根。

##### 配置
1. 该功能需要 Android Support Repository
2. build.gradle的dependencies中添加

		androidTestImplementation 'com.android.support.test.espresso:espresso-intents:3.0.2'
     
3. add intent test rule
	
    	@Rule
        public IntentsTestRule<MyActivity> intentsTestRule = new IntentTestRule<>(MyActivity.class);

4. match

	Hamcrest的匹配的条件是使用Hamcrest Matchers定义的，Hamcrest允许使用一下两种意图匹配器：
    
    - 使用现有的意图匹配器
    - 使用自己的意图匹配器

	Espresso-intents分别提供了意图验证和存根的方法intented()和intending().

	以下代码展示了使用启动浏览器的意图匹配的方式：

			intented(allOf(
    	    	hasActoin(equalTo(Intent.ACTION_VIEW)),
                hasCategories(hasItem(qualTo(Intent.CATEGORY_BROWSABLE))),
                hasData(hasHost(equalTo("www.google.com"))),
                hasExtras(allOf(hasEntry(equalTo("key1"), equalTo("value1")),
                toPackage("com.android.browser")))));	

5. validate intents

		intented(toPackage("com.android.phone"));
6. stubbing

		1. 构建结果以在启动特定活动时返回。
		2. 指示存根结果以响应意图
		3. 验证是否产生了预期的目标
		
        完整代码如下：
        	
            // Build the result to return when the activity is launched.
            Intent resultData = new Intent();
            String phoneNumber = "123-345-6789";
            resultData.putExtra("phone", phoneNumber);
            ActivityResult result =
                new ActivityResult(Activity.RESULT_OK, resultData);

            // Set up result stubbing when an intent sent to "contacts" is seen.
            intending(toPackage("com.android.contacts")).respondWith(result);

            // User action that results in "contacts" activity being launched.
            // Launching activity expects phoneNumber to be returned and displayed.
            onView(withId(R.id.pickButton)).perform(click());

            // Assert that the data we set up above is shown.
            onView(withId(R.id.phoneNumber)).check(matches(withText(phoneNumber)));

#### webview
[
Espresso Web](https://developer.android.google.cn/training/testing/espresso/web)

应该用不到吧，跳过了
#### idling resource
[闲置资源](https://developer.android.google.cn/training/testing/espresso/idling-resource)


## 总结
[Espresso小结备忘.pdf](https://android.github.io/android-test/downloads/espresso-cheat-sheet-2.1.0.pdf)