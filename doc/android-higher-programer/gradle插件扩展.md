## gradle插件扩展

我们已经学会了定义自定义gradle插件，但是我们还需要定义一些gradle插件里的属性，就像logger一样定义一个enable属性控制是否输出日志	

	logger {
    	enable false
    }
    
#### 基础用法 
像这样logger就是一个自定义的属性，其实是很简单的

	// 首先需要定义一个类，定义好属性和方法
    class LoogerExtension {
    	def enable = true
        
        def setMessage(String text) {
        	System.out.println(text)
        }
    }

	class HugoPlugin implements Plugin<Project> {
    	@Override
        void apply(Project project) {
        	...
        
        	project.extensions.create("logger", LoggerExtension)
        }
    }
    
    // 然后就可以在gradle中使用了
    logger {
    	enable false 或者 enable = false
        setMessage 'disable' 或者 message 'disable'
    }
    
#### 多个属性
如果要定义多个属性，例如要发布多平台就需要定义多个类似的属性

	platforms {
    	wandoujia {
        	name = 'xxx'
            api_key = 'xxx'
        }
        
        xiaomi {
        	name = 'xxx'
            api_key = 'xxx'
        }
    }

首先需要定义属性类

	class PlatformExtension {
    	def name
        def api_key
        
        /*
        需要string的构造函数，否则报错：
        A problem occurred evaluating project ':aop'.
        > Could not create an instance of type org.cmdmac.aopplugin.ListExtention.
           > Could not find any public constructor for class org.cmdmac.aopplugin.ListExtention which accepts parameters [java.lang.String].
		public PlatformExtension(String n) {
        	this.name = n
        }
    }
    
然后在Plugin中添加

    class PlatformPlugin implements Plugin<Project> {
		@Override
        void apply(Project porject) {
        	...
            
            def platforms = project.container(PlatformExtension)
            project.extensions.platform = platforms
        }
    }
    
使用：

    platforms {
            wandoujia {
                name = 'xxx'
                api_key = 'xxx'
            }

            xiaomi {
                name = 'xxx'
                api_key = 'xxx'
            }
        }
        
#### 属性嵌套
类似这样：

	android{
    	compileSdkVersion 19
        defaultConfig {
        	applicationId 'xxx'
        }
    }
    
首先定义Extension

	class AndroidExtension {
    	def Integer compileSdkVerion
        
        def DefaultConfigExtension defaultConfig
        
        public AndroidExtension (org.gradle.internal.impldep.org.sonatype.maven.polyglot.groovy.builder.factory.ObjectFactory objectFactory){
        	defaultConfig = objectFactory.newInstance(DefaultConfigExtension)
        }
        
        void defaultConfig(Action<? super DefaulConfigExtension> action){
        	action.execute(defaultConfig)
        }
        
        class DefaultConfigExtension {
        	def String applicationId
        }
    }
    
在Plugin中创建extension

	...
    project.extensions.create("android", AndroidExtension, project.objects)