# Gradle 

## Gradle是什么
1. gradle是一个工具 --> 会写，会配置脚本
2. gradle是一个编程框架 --> 更多内容

### Gradle的基本组件
gradle中，每一个待编译的工程都是一个project，每一个project构建时都包括一系列Task，比如一个android apk的编译包括java源码编译Task，资源编译Task，Jni编译Task，Lint检查Task，打包Apk的Task，签名Task等，这些Task的定义和执行是有插件决定的。**Gradle是一个框架，负责定义流程和规则，具体的编译工作是由Gradle插件完成的**，比如编译Java有Java插件，编译Groovy有Groovy插件，编译Android APP有Android APP插件，编译Android Library有Android Library插件。

举个栗子
![](http://img.blog.csdn.net/20150905194157628?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

这个gradle目录中包含5个项目，其中3个Lib项目和2个Apk项目，其中每一个项目的根目录下都有一个Build.gradle，build.gradle是该项目的编译脚本。要同时编译这些gradle的话，需要Multi-Projects Build。
这要需要以下步骤:

1. 在root目录下新建build.gradle。它用来配置其他子project，比如为子project添加一些属性。这个build.gradle可以没有。
2. 在root目录下新家settings.gradle。它来定义这个multiprojects包含哪些子project。这个settings.gradle是必须的，很重要！settings.gradle除了include外，还可以设置一些方法，用于这些函数会在gradle构建整个工程时执行。

### gradle命令介绍

### gradle的工作流程
![](http://img.blog.csdn.net/20150905194317170?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

1. 初始化阶段。 对于刚才的例子来说就是执行settings.gradle
2. 配置阶段。 解析根目录和每个项目的build.gradle。确定内部的Task关系和流程。
3. 执行阶段。

最后，关于gradle的工作流程，只需要记住：

* Gradle有一个初始化流程，这个时候settings.gradle会执行。
* 在配置阶段，每个Project都会被解析，其内部的任务也会被添加到一个有向图里，用于解决执行过程中的依赖关系。
* 然后才是执行阶段。你在gradle xxx中指定什么任务，gradle就会将这个xxx任务链上的所有任务全部按依赖顺序执行一遍！

### Gradle的编程模型和API实例
先看[官方文档](https://docs.gradle.org/current/dsl/)

#### Gradle对象

#### Project对象
在project中，我们要：

* 加载插件
* 如果插件不同，要对插件进行不同的配置
* 设置属性

#####1. 加载插件
apply plugin:xxx
#####2. 设置属性

#####3. Task介绍

#####4. 实例
* settings.gradle是必不可少的
* 根目录下的build.gradle。这个我们没讲过，因为有的根目录本身不包含代码，而是包含其他5个子project。
* 每个project目录下包含对于的build.gradle
* 另外，我把常用的函数封装到一个名为utils.gradle的脚本里了。

###### 4.1 utils.gradle
utils.gradle是自定义的，主要是添加一些常用的函数

[utils.gradle]

	import groovy.util.XmlSlurper  //解析XML时候要引入这个groovy的package  
   
	def copyFile(String srcFile,dstFile){  
	     ......//拷贝文件函数，用于将最后的生成物拷贝到指定的目录  
	}  
	def rmFile(String targetFile){  
	    .....//删除指定目录中的文件  
	}  
	   
	def cleanOutput(boolean bJar = true){  
	    ....//clean的时候清理  
	}  
	   
	def copyOutput(boolean bJar = true){  
	    ....//copyOutput内部会调用copyFile完成一次build的产出物拷贝  
	}  
	   
	def getVersionNameAdvanced(){//老朋友  
	   defxmlFile = project.file("AndroidManifest.xml")  
	   defrootManifest = new XmlSlurper().parse(xmlFile)  
	   returnrootManifest['@android:versionName']    
	}  
	   
	//对于android library编译，我会disable所有的debug编译任务  
	def disableDebugBuild(){  
	  //project.tasks包含了所有的tasks，下面的findAll是寻找那些名字中带debug的Task。  
	  //返回值保存到targetTasks容器中  
	  def targetTasks = project.tasks.findAll{task ->  
	     task.name.contains("Debug")  
	  }  
	  //对满足条件的task，设置它为disable。如此这般，这个Task就不会被执行  
	 targetTasks.each{  
	     println"disable debug task  :${it.name}"  
	    it.setEnabled false  
	  }  
	}  
	//将函数设置为extra属性中去，这样，加载utils.gradle的Project就能调用此文件中定义的函数了  
	ext{  
	    copyFile= this.&copyFile  
	    rmFile =this.&rmFile  
	   cleanOutput = this.&cleanOutput  
	   copyOutput = this.&copyOutput  
	   getVersionNameAdvanced = this.&getVersionNameAdvanced  
	   disableDebugBuild = this.&disableDebugBuild  
	}

###### 4.2 settings.gradle
内容为include的项目和一些初始化操作

[settings.gradle]

	/*我们团队内部建立的编译环境初始化函数 
	  这个函数的目的是 
	  1  解析一个名为local.properties的文件，读取AndroidSDK和NDK的路径 
	  2  获取最终产出物目录的路径。这样，编译完的apk或者jar包将拷贝到这个最终产出物目录中 
	  3 获取Android SDK指定编译的版本 
	*/  
	def initMinshengGradleEnvironment(){  
	    println"initialize Minsheng Gradle Environment ....."  
	   Properties properties = new Properties()  
	   //local.properites也放在posdevice目录下  
	    FilepropertyFile = new File(rootDir.getAbsolutePath()+ "/local.properties")  
	   properties.load(propertyFile.newDataInputStream())  
	    /* 
	      根据Project、Gradle生命周期的介绍，settings对象的创建位于具体Project创建之前 
	      而Gradle底对象已经创建好了。所以，我们把local.properties的信息读出来后，通过 
	     extra属性的方式设置到gradle对象中 
	      而具体Project在执行的时候，就可以直接从gradle对象中得到这些属性了！ 
	    */  
	    gradle.ext.api =properties.getProperty('sdk.api')  
	    gradle.ext.sdkDir =properties.getProperty('sdk.dir')  
	     gradle.ext.ndkDir =properties.getProperty('ndk.dir')  
	     gradle.ext.localDir =properties.getProperty('local.dir')  
	    //指定debugkeystore文件的位置，debug版apk签名的时候会用到  
	    gradle.ext.debugKeystore= properties.getProperty('debug.keystore')  
	     ......  
	    println"initialize Minsheng Gradle Environment completes..."  
	}  
	//初始化  
	initMinshengGradleEnvironment()  
	//添加子Project信息  
	include 'CPosSystemSdk' , 'CPosDeviceSdk' ,'CPosSdkDemo','CPosDeviceServerApk', 'CPosSystemSdkWizarPosImpl'  

###### 4.3 build.gradle
全局配置

[build.gradle]

	//下面这个subprojects{}就是一个Script Block  
	subprojects {  
	  println"Configure for $project.name" //遍历子Project，project变量对应每个子Project  
	  buildscript {  //这也是一个SB  
	    repositories {//repositories是一个SB  
	       ///jcenter是一个函数，表示编译过程中依赖的库，所需的插件可以在jcenter仓库中  
	       //下载。  
	       jcenter()  
	    }  
	    dependencies { //SB  
	        //dependencies表示我们编译的时候，依赖android开发的gradle插件。插件对应的  
	       //class path是com.android.tools.build。版本是1.2.3  
	        classpath'com.android.tools.build:gradle:1.2.3'  
	    }  
	   //为每个子Project加载utils.gradle 。当然，这句话可以放到buildscript花括号之后  
	   applyfrom: rootProject.getRootDir().getAbsolutePath() + "/utils.gradle"  
	 }//buildscript结束  
	}  

###### 4.4 重要的Script Block

![](http://img.blog.csdn.net/20150905194626346?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

某些Script Block的解释

* subprojects：它会遍历posdevice中的每个子Project。在它的Closure中，默认参数是子Project对应的Project对象。由于其他SB都在subprojects花括号中，所以相当于对每个Project都配置了一些信息。
* buildscript：它的closure是在一个类型为ScriptHandler的对象上执行的。主意用来所依赖的classpath等信息。通过查看ScriptHandler API可知，在buildscript SB中，你可以调用ScriptHandler提供的repositories(Closure )、dependencies(Closure)函数。这也是为什么repositories和dependencies两个SB为什么要放在buildscript的花括号中的原因。明白了？这就是所谓的行话，得知道规矩。不知道规矩你就乱了。记不住规矩，又不知道查SDK，那么就彻底抓瞎，只能到网上到处找答案了！
* 关于repositories和dependencies，大家直接看API吧。后面碰到了具体代码我们再来介绍

###### 4.5 依赖项目的build.gradle
如果该项目是一个android library。android studio默认的build方式编译得到的是一个.aar文件，如果需求是生成.jar格式的文件，就要按下边的配置来。

[build.gradle]

	//Library工程必须加载此插件。注意，加载了Android插件就不要加载Java插件了。因为Android  
	//插件本身就是拓展了Java插件  
	apply plugin: 'com.android.library'   
	//android的编译，增加了一种新类型的ScriptBlock-->android  
	android {  
	       //你看，我在local.properties中设置的API版本号，就可以一次设置，多个Project使用了  
	      //借助我特意设计的gradle.ext.api属性  
	       compileSdkVersion =gradle.api  //这两个红色的参数必须设置  
	       buildToolsVersion  = "22.0.1"  
	       sourceSets{ //配置源码路径。这个sourceSets是Java插件引入的  
	       main{ //main：Android也用了  
	           manifest.srcFile 'AndroidManifest.xml' //这是一个函数，设置manifest.srcFile  
	           aidl.srcDirs=['src'] //设置aidl文件的目录  
	           java.srcDirs=['src'] //设置java文件的目录  
	        }  
	     }  
	   dependencies {  //配置依赖关系  
	      //compile表示编译和运行时候需要的jar包，fileTree是一个函数，  
	     //dir:'libs'，表示搜索目录的名称是libs。include:['*.jar']，表示搜索目录下满足*.jar名字的jar  
	     //包都作为依赖jar文件  
	       compile fileTree(dir: 'libs', include: ['*.jar'])  
	   }  
	}  //android SB配置完了  
	//clean是一个Task的名字，这个Task好像是Java插件（这里是Android插件）引入的。  
	//dependsOn是一个函数，下面这句话的意思是 clean任务依赖cposCleanTask任务。所以  
	//当你gradle clean以执行clean Task的时候，cposCleanTask也会执行  
	clean.dependsOn 'cposCleanTask'  
	//创建一个Task，  
	task cposCleanTask() <<{  
	    cleanOutput(true)  //cleanOutput是utils.gradle中通过extra属性设置的Closure  
	}  
	//前面说了，我要把jar包拷贝到指定的目录。对于Android编译，我一般指定gradle assemble  
	//它默认编译debug和release两种输出。所以，下面这个段代码表示：  
	//tasks代表一个Projects中的所有Task，是一个容器。getByName表示找到指定名称的任务。  
	//我这里要找的assemble任务，然后我通过doLast添加了一个Action。这个Action就是copy  
	//产出物到我设置的目标目录中去  
	tasks.getByName("assemble"){  
	   it.doLast{  
	       println "$project.name: After assemble, jar libs are copied tolocal repository"  
	        copyOutput(true)  
	     }  
	}  
	/* 
	  因为我的项目只提供最终的release编译出来的Jar包给其他人，所以不需要编译debug版的东西 
	  当Project创建完所有任务的有向图后，我通过afterEvaluate函数设置一个回调Closure。在这个回调 
	  Closure里，我disable了所有Debug的Task 
	*/  
	project.afterEvaluate{  
	    disableDebugBuild()  
	}  

android定义的script

![](http://img.blog.csdn.net/20150905194701217?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![](http://img.blog.csdn.net/20150905194720267?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

其中buildToolsVersion和compileSdkVersion是必须配置的

###### 4.6 APK项目的build.gradle
一个apk的build，包括ndk的编译，项目签名，混淆，配置依赖等。

[build.gradle]

	apply plugin: 'com.android.application'  //APK编译必须加载这个插件  
	android {  
	      compileSdkVersion gradle.api  
	      buildToolsVersion "22.0.1"  
	      sourceSets{  //差不多的设置  
	       main{  
	           manifest.srcFile 'AndroidManifest.xml'  
	          //通过设置jni目录为空，我们可不使用apk插件的jni编译功能。为什么？因为据说  
	         //APK插件的jni功能好像不是很好使....晕菜  
	          jni.srcDirs = []   
	           jniLibs.srcDir 'libs'  
	            aidl.srcDirs=['src']  
	           java.srcDirs=['src']  
	           res.srcDirs=['res']  
	        }  
	    }//main结束  
	   signingConfigs { //设置签名信息配置  
	       debug {  //如果我们在local.properties设置使用特殊的keystore，则使用它  
	           //下面这些设置，无非是函数调用....请务必阅读API文档  
	           if(project.gradle.debugKeystore != null){  
	              storeFile file("file://${project.gradle.debugKeystore}")  
	              storePassword "android"  
	              keyAlias "androiddebugkey"  
	              keyPassword "android"  
	           }  
	        }  
	   }//signingConfigs结束  
	     buildTypes {  
	       debug {  
	           signingConfig signingConfigs.debug  
	           jniDebuggable false  
	        }  
	    }//buildTypes结束  
	   dependencies {  
	        //compile：project函数可指定依赖multi-project中的某个子project  
	       compile project(':CPosDeviceSdk')  
	       compile fileTree(dir: 'libs', include: ['*.jar'])  
	   } //dependices结束  
	  repositories{  
	   flatDir {//flatDir：告诉gradle，编译中依赖的jar包存储在dirs指定的目录  
	           name "minsheng-gradle-local-repository"  
	            dirsgradle.LOCAL_JAR_OUT //LOCAL_JAR_OUT是我存放编译出来的jar包的位置  
	   }  
	  }//repositories结束  
	}//android结束  
	/* 
	   创建一个Task，类型是Exec，这表明它会执行一个命令。我这里让他执行ndk的 
	   ndk-build命令，用于编译ndk。关于Exec类型的Task，请自行脑补Gradle的API 
	*/  
	//注意此处创建task的方法，是直接{}喔，那么它后面的tasks.withType(JavaCompile)  
	//设置的依赖关系，还有意义吗？Think！如果你能想明白，gradle掌握也就差不多了  
	task buildNative(type: Exec, description: 'CompileJNI source via NDK') {  
	       if(project.gradle.ndkDir == null) //看看有没有指定ndk.dir路径  
	          println "CANNOT Build NDK"  
	       else{  
	            commandLine "/${project.gradle.ndkDir}/ndk-build",  
	               '-C', file('jni').absolutePath,  
	               '-j', Runtime.runtime.availableProcessors(),  
	               'all', 'NDK_DEBUG=0'  
	        }  
	  }  
	 tasks.withType(JavaCompile) {  
	       compileTask -> compileTask.dependsOn buildNative  
	  }  
	  ......    
	 //对于APK，除了拷贝APK文件到指定目录外，我还特意为它们加上了自动版本命名的功能  
	 tasks.getByName("assemble"){  
	       it.doLast{  
	       println "$project.name: After assemble, jar libs are copied tolocal repository"  
	       project.ext.versionName = android.defaultConfig.versionName  
	       println "\t versionName = $versionName"  
	       copyOutput(false)  
	     }  
	}  

## 参考
[http://blog.csdn.net/innost/article/details/48228651](http://blog.csdn.net/innost/article/details/48228651)