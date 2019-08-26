## FBReader源码分析-menu菜单
#### 1. 点击屏幕中部显示菜单流程

#### 2. 显示菜单内容
在上一节的分析中我们已经知道，菜单的显示在页面的oncreateoptionmenu中完成的。
    
	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		super.onCreateOptionsMenu(menu);

		setupMenu(menu);

		return true;
	}

这个方法代码很少,知识简单的调用了setupMenu(menu)方法。

	private void setupMenu(Menu menu) {
		final String menuLanguage = ZLResource.getLanguageOption().getValue();
		if (menuLanguage.equals(myMenuLanguage)) {
			return;
		}
		myMenuLanguage = menuLanguage;

		menu.clear();
		fillMenu(menu, MenuData.topLevelNodes());
		synchronized (myPluginActions) {
			int index = 0;
			for (PluginApi.ActionInfo info : myPluginActions) {
				if (info instanceof PluginApi.MenuActionInfo) {
					addMenuItem(
						menu,
						PLUGIN_ACTION_PREFIX + index++,
						((PluginApi.MenuActionInfo)info).MenuItemName
					);
				}
			}
		}

		refresh();
	}
   
这个方法中，MenuData.getTopLevelNodes()即是获取菜单条目数据的方法，fillMenu()是遍历这些条目，并调用addMenuItem()将条目设置给Android optionmenu

	private void addMenuItem(Menu menu, String actionId, Integer iconId, String name) {
		if (name == null) {
			name = ZLResource.resource("menu").getResource(actionId).getValue();
		}
		final MenuItem menuItem = menu.add(name);
		if (iconId != null) {
			menuItem.setIcon(iconId);
		}
		menuItem.setOnMenuItemClickListener(myMenuListener);
		myMenuItemMap.put(menuItem, actionId);
	}
包括设置name，icon，以及点击事件。其中name是从assert文件中加载的，用的是sax解析。

回到setmenu方法中，填充完menu会遍历myPluginActions集合，然后将myPluginActions的item添加到menu中。

在addMenuItem中，会给每个条目设置点击事件，然后将item和actionid键值对保存到myMenuItemMap中。这个mymenulistener就是提供actionidid，然后由myIdToActionMap找到对应的actionid响应点击事件。

myIdToActionMap添加数据是在ZLApplication.addAction()方法，这个方法的调用有几个地方，1，fbreader.myPluginInfoReceiver.onreceive 2， fbreader.oncreate 3. fbreaderapp的构造方法中。也就是说基本上这些事件都是在应用初始化的时候都定义好了。

#### 3. 设置相关(以设置翻页方式为例)
在上一节我们分析了菜单条目的加载，主要就是要找到菜单对应的action，我们在fbreader.oncreate方法中发现ActionCode.SHOW_PREFERENCES即为我们要找的阅读设置相关设置
	
    myFBReaderApp.addAction(ActionCode.SHOW_PREFERENCES, new ShowPreferencesAction(this, myFBReaderApp));
        
在这个action的run方法中我们发现PreferenceActivity即是我们要找的activity文件。

在它的父类的oncreate()方法中

	@Override
	protected void onCreate(Bundle bundle) {
		super.onCreate(bundle);

		// 异常捕捉
		Thread.setDefaultUncaughtExceptionHandler(new org.geometerplus.zlibrary.ui.android.library.UncaughtExceptionHandler(this));

		// 创建数据库
		SQLiteCookieDatabase.init(this);

		// 创建preferencescreen
		myScreen = getPreferenceManager().createPreferenceScreen(this);

		final Intent intent = getIntent();
		final Uri data = intent.getData();
		final String screenId;
		if (Intent.ACTION_VIEW.equals(intent.getAction())
				&& data != null && "fbreader-preferences".equals(data.getScheme())) {
			screenId = data.getEncodedSchemeSpecificPart();
		} else {
			screenId = intent.getStringExtra(SCREEN_KEY);
		}

		Config.Instance().runOnConnect(new Runnable() {
			public void run() {
				// 抽象方法，子类初始化
				init(intent);

				// 获取要显示的screen
				final Screen screen = myScreenMap.get(screenId);
				
				// 设置preferencescreen
				setPreferenceScreen(screen != null ? screen.myScreen : myScreen);
			}
		});
	}
    
设置页面采用了sharepreference的方式，并且采用过时的方法先获取preferencescreen再设置preferencescreen，这与我们常用的addPreferenceFromResource一样。

init()方法是抽象方法，具体实现就是PreferenceActivity实现的，这个方法中的代码很多，但是主要做的都是创建各种preference，并把preference添加到preferscreen。

#### FBReader默认参数设置
我们要在项目中使用fbreader的话，必须要对它做自己的处理，例如修改默认字体，默认背景，翻页方式，默认字体大小等等，上一节我们了解了fbreader是如何设置翻页方式的，我们讲到在init方法中会创建很多的preferencescreen，其中翻页的菜单对应的就是scrollingScreen，

		scrollingScreen.addOption(pageTurningOptions.Animation, "animation");
		scrollingScreen.addPreference(new AnimationSpeedPreference(
			this,
			scrollingScreen.Resource,
			"animationSpeed",
			pageTurningOptions.AnimationSpeed
		));
		scrollingScreen.addOption(pageTurningOptions.Horizontal, "horizontal");

设置翻页的代码就这么多，一眼就能看出来第一行scrollingScreen.addOption(pageTurningOptions.Animation, "animation");就是我们要找的翻页方式设置，

	public <T extends Enum<T>> Preference addOption(ZLEnumOption<T> option, String key) {
			return addPreference(
				new ZLEnumPreference<T>(ZLPreferenceActivity.this, option, Resource.getResource(key))
			);
		}
ZLEnumPreference是ListPreference的子类，用于显示列表菜单，相当于现在的radiugroup，option用于设置初始值，key用于加载对应的选项列表。

我们知道了这个PageTurningOptions对象是用来设置初始值的，然后我们ctrl+鼠标左键看到，这个类是在FBReaderApp中实例化的，

	public class PageTurningOptions {
	public static enum FingerScrollingType {
		byTap, byFlick, byTapAndFlick
	}
	public final ZLEnumOption<FingerScrollingType> FingerScrolling =
		new ZLEnumOption<FingerScrollingType>("Scrolling", "Finger", FingerScrollingType.byTapAndFlick);

	// 设置默认翻页无动画
    //	public final ZLEnumOption<ZLView.Animation> Animation =
    //		new ZLEnumOption<ZLView.Animation>("Scrolling", "Animation", ZLView.Animation.slide);
	public final ZLEnumOption<ZLView.Animation> Animation =
		new ZLEnumOption<ZLView.Animation>("Scrolling", "Animation", ZLView.Animation.none);
	public final ZLIntegerRangeOption AnimationSpeed =
		new ZLIntegerRangeOption("Scrolling", "AnimationSpeed", 1, 10, 7);

	public final ZLBooleanOption Horizontal =
		new ZLBooleanOption("Scrolling", "Horizontal", true);
	public final ZLStringOption TapZoneMap =
		new ZLStringOption("Scrolling", "TapZoneMap", "");
	}

所以就可以做如上的修改了。
其实打开PageTurningOption所在的目录，这个option目录下还包括ViewOption，ImageOption等，所以很多默认参数修改都可以在这里做。
    