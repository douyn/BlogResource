## Binder机制-应用-AIDL
1. 创建aidl文件。在Android Studio中可以右键新建ITestService.aidl文件，添加接口。注意：支持的数据类型为java的基本数据类型，String，charsequence,list,map
2. 自动生成对应的java文件。重新编译项目或者sync都会自动在app/build/generated/source/aidl/debug/目录下生成ITestService.aidl对应的.java文件。
3. 编写service代码。新建service类，在onBind中返回ITestService.stub对象。在创建该对象时，实现ITestService.aidl中的方法。注意添加服务到manifast，设置服务exported=true,process="xx"
4. 编写client代码，service connection和bindservice。创建serviceconnection,在onServiceConnected中，先将binder转换为ITestService,然后调用接口，最后bindService