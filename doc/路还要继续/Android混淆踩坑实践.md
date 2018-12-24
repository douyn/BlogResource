## Android混淆踩坑实践

一般情况下，网上的大多数模板都是可用的，如果出错通常需要特殊的指定要不去混淆的类或方法等。

两种混淆错误，一种是编译时报错，一种是运行时报错。
编译时错误一般是某些类需要混淆。
运行时错误一般是库调用java代码出错。


#### 1. 耐心看log，一般根据log就可以定位到错误的地方。
跟Android项目中的warning不一样这里的warning也会导致编译不过去

        Warning: Exception while processing task java.io.IOException: Please correct the above warnings first.
        :fBReader:transformClassesAndResourcesWithProguardForRelease FAILED

        FAILURE: Build failed with an exception.
        
        
        遇到这种情况就滑到上边看log里的最上边的出现的warning
        
        Note: the configuration refers to the unknown class 'android.webkit.webViewClient'
        Note: the configuration refers to the unknown class 'android.webkit.webViewClient'
        Note: the configuration refers to the unknown class 'android.webkit.webView'
        Note: the configuration refers to the unknown class 'jav.lang.String'
              Maybe you meant the fully qualified name 'java.lang.String'?
        Note: the configuration refers to the unknown class 'org.greenrobot.greendao.AbstractDao'
        Warning: com.xxx.xxx: can't find superclass or interface android.xxx.xxx
        
        说明com.xxx.xxx这个类出现了warning，在proguard.pro文件中添加
        
        	-dontwarn com.xx.xx即可

#### 2. 如果混淆之后打包编译都正常，千万不要以为万事大吉，一定要安装查看运行时基本功能是否正常 
	
混淆之后经常出现运行时错误，也是需要去看应用crash的log，一行一行分析定位，实在不行就去google。

##### 1、ClassNotFoundException，NoSuchMethodError
找不到类和方法的原因很多，需要根据log去定位。

     Didn't find class "xxx" on path: DexPathList[[zip file "/data/app/xxx/base.apk"],nativeLibraryDirectories=[/data/app/xxx/lib/arm, /vendor/lib, /system/lib]]
     
     然后java的loadLibrary方法会报错，这个问题乍一看可能以为是so库的问题，你可能回去不混淆so等，但是其实不用，因为so默认就是不混淆的，问题在于jni调用java类时，java类的路径因为混淆而发生变化，所以你需要去不去混淆这个类。
     
     -keep class xxx{*;}
     
     除了这种jni调用java会出现classnotfoundexception之外，本地代码通过反射调用其他的类等也会出现上述问题，只要分析log找到对应的类不混淆即可
     
 #### 2、ExceptionInInitializerError
 
       原因：这是由于类初始化的时候发生了异常。
       
       解决办法：找到具体是哪里的类哪个方法哪个类初始化的时候发生的异常，然后解决问题。

       注：遇到这个错误，首先要确认是不是因为第三方的jar包导致的。如果不是的话，就找本地代码，看是不是写的有问题。如果确实是因为第三方jar包的代码导致的，尽量找到源码或者反编译，查看问题到底是什么引起的，然后找到相应的配置在proguard里面配置。
      
       例如：我们项目中碰到过一个问题，就是因为第三方的jar包里面有一个字段初始化的时候报了空指针，然后导致我们的代码报了上面的错。当时很奇怪，为什么第三方的jar包还能报错，最后调查了之后才发现，是因为人家用到了类的注解，而proguard在混淆优化的时候把注解去掉了，所以报了空指针，只需要在proguard里面加上保护注解就可以了-keepattributes *Annotation*
#### 3、 ClassCastException
       原因：类强制转换的时候出错。
       
       解决办法：找到代码，看是代码写的问题，还是混淆后的问题。如果没有混淆正常运行的话，一般都是因为混淆后遇到了各种问题才报的错。我们项目中遇到的问题是因为没有让proguard保持泛型，所以强转的时候报错。只需要在proguard文件里面加上泛型即可-keepattributes Signature
       
#### 4、Resources$NotFoundException(resource not found)

资源没有找到，是因为第三方jar包或者自己的代码是通过反射获得R文件中的资源，所以需要将R文件屏蔽掉

    原因：代码进行了混淆，R文件没有了，所以通过反射获取的R文件找不到
    
    解决办法：在proguard文件里设置不混淆R文件    -keep class **.R$* { *; }
            
       

