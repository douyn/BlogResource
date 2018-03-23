## Android Studio引用framework.jar
#### 前言
做系统开发感觉还是用eclipse比较好，另外compile-sdk-version最好提前设置好，不要设置的太高，因为我在开始做项目的时候没有告知Android版本，设置的比较高，导致项目中有的api不能用，所以要重新写功能。

如果做系统应用开发，可以将应用源码在Android源码里编译，但是因为两点我推荐使用预置apk的方式：
	
    1. 因为我的项目是Android studio项目，也就是说用gradle构建的，项目中也是用了很多gradle配置，如果要将应用源码导入Android源码中编译，我不得不先将项目迁移到eclipse，并且配置编译环境
    2. 如果是内置源码，就需要维护两份代码，而且如果应用做了修改，就要就要先在eclipse上调试成功，再在编译过Android之后，再次调试。
    3. 如果修改了framework层的代码，验证起来也是比较麻烦。

#### 步骤

1. 编译classes.jar。

	- 修改完framework源码，使用make -j5 framework编译framework模块。
	- 对于user版本和dubug版本，生成的路径分别是out/target/product/$(DEVICE)/system/framework/framework.jar和out/target/common/obj/JAVA_LIBRARY/framework_interminate/classes.jar。将这个jar包拷贝到项目libs目录下。注意，1我这里只试过JAVA_LIBRARY目录下的classes.jar。2在Android4.4上使用的时候发现framework的代码其实被编译为两部分，framework.jar和framework2.jar。其中framework.jar对应framework/base/core/java/android/也就是我们日常用的api，framework2.jar对应framework/base/core/java/com和/javax，这里主要是一些Android内部的api和类文件，一般都有点问题没有被公开，但是正好我项目里就用到了TAT,这里要说的是framework.jar和framework2.jar对应out/target/common/obj/JAVA_LIBRARY/framework_intermediates和out/target/common/obj/JAVA_LIBRARY/framework2_intermediates中的classes.jar文件。如果你要用framework2.jar中的api，做法是跟framework.jar的方式是一样的。
2. 项目中引用framework.jar并配置。

		1. 在Android studio项目里刚才复制的jar文件上右键add as library，此时会在mudule的build.gradle文件中的dependencies中自动生成compile files('libs/frameworks.jar')
		2. 将这行代码放在dependencies的开头，并将compile改为provided。这里是设置生成apk时不将这个jar编译进去。
		3. 在project的build.gradle中的allprojects下添加
		  
                gradle.projectsEvaluated {
                    tasks.withType(JavaCompile) {
            //            options.compilerArgs.add('-Xbootclasspath/p:app\\libs\\frameworks.jar')
                        options.compilerArgs << '-Xbootclasspath/p:app/libs/frameworks.jar'
                    }
                }
            
3. 编译运行。这里在Android studio中修改过的api还是会想显示红色，但是可以编译运行。