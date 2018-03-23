## 在Android studio中调试aosp源码

1. 可选，修改Android studio的内存配置

		配置文件位置为:
        IDEA的是IDEA_HOME/bin/idea.vmoptions
		AS的是32位电脑AS_HOME/bin/studio.vmoptions,64位电脑AS_HOME/bin/studio64.vmoptions
        
        在对应设置修改为
        -Xms748m 和 -Xmx748m
2. 可选，配置as的jdk、sdk

		添加空的SDK的原因是AOSP是以项目的形式导入的，而IDEA/AS的项目至少需要一个SDK，否则会报错。
        
        而我们使用空SDK就解决了这个错误并且在代码跳转时不会跳转到桌面版的Java SDK中，而是在Android的源代码里。
        
		我们可以创建一个1.7 (No Libraries)的JDK，然后将它classpath中所有的jar都删掉。
3. 必选，生成导入AS所需要的配置文件(android.ipr)

		1. 编译源码，如果是编译过的源码省略该步骤，注意aosp如果没有编译过是不会生成下一步的idegen.jar文件。
		2. 执行mmm development/tool/idegen/,执行该命令生成idegen.jar文件
		3.  执行sh development/tool/idegen/idegen.sh，执行该命令生成android.ipr和android.iml文件。
4. 必选，导入源码。

		1. 可选，修改android.iml文件，将自己用不到的代码exclude出去。
            <excludeFolder url="file://$MODULE_DIR$/.repo" />
            <excludeFolder url="file://$MODULE_DIR$/abi" />
            <excludeFolder url="file://$MODULE_DIR$/art" />
		2. 通过Android studio菜单栏File->open->android.ipr导入aosp项目
5. 为当前工程配置module依赖

    1. 可选，为当前工程设置正确的sdk和jdk。
    
    2. 如果要debug源码是必须的，设置modules的依赖。
    

6. debug源码


###### 参考
[如何使用Android Studio开发/调试Android源码](https://www.linuxidc.com/Linux/2015-04/116459.htm)