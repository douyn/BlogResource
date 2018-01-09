## AIDL
aidl全称Android接口定义语言
* 为什么要定义这个语言
* aidl语法是什么
* 如何使用aidl进行进程间通信
#### 为什么要定义AIDL，以及AIDL的应用场景
进程间通信，通过aidl来制定一些规则规定数据格式等

#### AIDL语法
* 文件类型，aidl文件类型是.aidl而不是.java
* 数据结构。aidl中除了默认支持的数据类型，其它数据类型都需要导包。比如自定义的bean类需要导包。默认数据类型有java中的八种基本数据类型，String,CharSequence,List,Map
* 定向tag，in，out，inout
* 数据分类，一种是数据型，一种是接口型
#### 使用AIDL的步骤
1. 使数据实现Parceable接口
2. 编写aidl文件
3. 编写service文件