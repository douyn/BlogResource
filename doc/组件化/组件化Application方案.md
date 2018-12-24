# 组件化Application方案

### 前言
本人也是刚接触组件化不久，原项目是由几个分开的系统应用组成，目前做的只是将几个应用合并到同一项目下，将这几个应用拆分为几个模块并将其中通用的地方抽出来新建了几个基础组件和lib组件。

在这个过程中也踩了很多坑。总结下来主要是：
1. 全局gradle配置
2. 对于集成状态和组件状态的区别处理
3. arouter的使用
4. 一些第三方的特殊处理。我遇到的主要是butterknife。主要包括不使用apt改用anotationprocess，使用butterknife-gradle-plugin-8.4.0,全局修改R为R2，注意在R2下项目也可以作为application运行。
5. 应用application

### Application方案
1. 创建base module。该module提供基础类库。
2. 创建BaseApplication类，ModuleConfig类以及BaseApplicationImpl类。BaseApplication类的onCreate()方法中初始化一些全局配置并且初始化模块配置。BaseApplicationImpl类是一个接口类，需要各模块自己去实现各个模块的配置。这些配置的类是定义在ModuleConfig中，在初始化的时候会通过反射创建这些类。

		// ------------BaseApplication------------
        public class BaseApplication extends MultiDexApplication {

            @Override
            public void onCreate() {
                super.onCreate();

                // 初始化全局配置
                initGlobalConfig();

                // 初始化模块配置
                initModuleConfit();
            }

            private void initGlobalConfig() {
                // 初始化ARouter
                initARouter();
            }

            private void initModuleConfit() {
                for (String modules : ModuleConfig.MODULELIST){
                    try {
                        Class clz = Class.forName(modules);
                        Object obj = clz.newInstance();
                        if (obj instanceof BaseApplicationImp){
                            ((BaseApplicationImp) obj).onCreate(this);
                        }

                    } catch (ClassNotFoundException e) {
                        e.printStackTrace();
                    } catch (IllegalAccessException e) {
                        e.printStackTrace();
                    } catch (InstantiationException e) {
                        e.printStackTrace();
                    }
                }
            }

            private void initARouter() {
                ARouter.openLog();
                ARouter.openDebug();
                ARouter.init(this);
            }
        }
        
		// ------------BaseApplicationImp------------
        public interface BaseApplicationImp{
            void onCreate(Application application);
        }
        
        // ------------ModuleConfig-------------
        public interface ModuleConfig {

            static final String MODULE_PDFREADER = "com.dou.sample.mupdf_demo.BaseApplication";
            static final String MODULE_LAUNCHER = "com.guowen.luncher.guowenreaderapp.app.AppContext";

            public static final String[] MODULELIST = {
                    MODULE_LAUNCHER,
                    MODULE_PDFREADER
            };
        }
        
3. 在各个子module中实现BaseApplicationImpl，这个类可以提供模块化的配置以及application context对象。清单文件都设置为BaseApplication即可。

### 思路
原来的项目是每个应用都有一个application和baseapplication，现在baseapplication抽出来了，但是集成状态下只能用同一个application，组件状态下application也不同。

所以这里做了统一处理。不管是集成状态还是组件状态都设置application为baseapplication。baseapplication的oncreate方法主要有两个方法，加载全局配置和加载组件配置。加载全局配置比较简单，加载组件配置就是将组件的配置类在moduleconfig类中声明，然后通过遍历这些配置列表，通过反射加载baseapplicationimpl的实现类。如果是组件状态下直接会报找不到类异常也就不会加载别的module的配置。

因为之前项目的application类(单例模式)不仅会初始化配置还会提供一些静态成员和全局context，如果改动，项目代码也要修改很多。所以这里直接用这个类实现BaseApplicationImpl，将这个类的单例context设置baseapplication传递的对象。


