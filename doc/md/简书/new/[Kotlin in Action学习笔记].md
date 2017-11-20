# [Kotlin In Action学习笔记]
## 前言
#### 使用kotlin进行android开发
kotlin非常适合开发android应用程序，他将现代语言的所有优势带入android平台并且不会引入新的限制:
* 兼容性。kotlin与jdk6 完全兼容，并且主流IDE都支持kotlin语言。
* 性能。kotlin采用和java类似的字节码结构，并且支持lambda等表达式使得kotlin与java的代码运行类似或者更快。
* 互操作性。kot;in与java可以100%的互操作。允许使用现有的android库。
* 学习曲线。kotlin对于java开发者入门十分简单，IDE中的转换器有助于开发者迈出第一步。
* 占用。kotlin具有紧凑的运行时库，并且可以通过Proguard进一步减少。
* 编译。kotlin支持高效的增量编译。

#### 用于android开发的工具
* [kotlin android扩展](https://www.kotlincn.net/docs/tutorials/android-plugin.html)是一个编译器扩展，可以让你摆脱编译器中的findviewbyid()
* [anko](http://github.com/kotlin/anko)是一个围绕android api的kotlin友好的包装器的库，以及可以使用kotlin代码替换布局文件.xml的dsl

#### android开发入门
现在android studio/eclipse/intellij idea都已经支持kotlin，其中android　studio在3.0版本以后已经默认支持kotlin，用户不用再下载kotlin插件，eclipse如何使用请自行google，android studio3.0以前版本在settings-->plugins-->install jetbrains plugins搜索kotlin下载。下载之后重启android studio，创建新项目完毕后，点击菜单code-->convert java code to kotlin即可将项目自动生成的java代码转换成kotlin代码。在菜单tools-->kotlin-->configure kotlin in project，选择module和kotlin版本，一般直接确定即可。

#### kotlin android扩展
kotlin官方团队的kotlin android扩展改进了对android支持，可以提高开发体验。

开发者仅需要在module级别的build.gradle中添加

		apply plugin: 'kotlin-android-extensions'
        
配置了之后，在xml文件中定义了id，在activity/fragment中，输入这个id，android studio的代码联想会自动联想到对应的资源文件，并自动添加依赖

	import kotlinx.android.synthetic.main.＜布局＞.*
    
这样就省去了大量的findviewbyid代码。

#### kotlin 对android　框架的支持
##### ButterKnife

ButterKnife可以直接将view和变量进行绑定从而免去调用findViewById。

另外，Kotlin Android 扩展插件（Android Studio 内置)具有同样的效果：使用简洁明了的代码替换findViewByid。 除非现在你正在使用 ButterKnife 而且没有迁移计划，那么前者非常值得尝试。

在 Kotlin 中使用 ButterKnife 与 Java 中完全一致。 在 Gradle 构建脚本的修改如下，后面将重点介绍代码部分的差异。

在 Gradle 依赖中添加 kotlin-kapt 插件，并使用 kapt 替代 annotationProcessor。

    apply plugin: 'kotlin-kapt'

    dependencies {
        ...
        compile "com.jakewharton:butterknife:$butterknife-version"
        kapt "com.jakewharton:butterknife-compiler:$butterknife-version"
    }
我们已经将整个 ButterKnife 示例代码转换为 Kotlin， 参见详细代码。

让我门看看发生了什么变化。 在 Java 中使用注解对将变量与之对应的 view 进行绑定：

	@BindView(R2.id.title) TextView title;
在 Kotlin 中使用属性而不是直接使用变量。 对属性使用注解:

    @BindView(R2.id.title)
    lateinit var title: TextView
@BindView 被定义为仅应用于变量字段，而将注解应用于整个属性时，Kotlin 编译器能够理解并且覆盖相应注解的字段。

lateinit 修饰符允许声明非空类型，并在对象创建后(构造函数调用后)初始化。 不使用 lateinit 则需要声明可空类型并且有额外的空安全检测操作。

使用 ButterKnife 注解可以将方法设置为监听器：

    @OnClick(R2.id.hello)
    internal fun sayHello() {
        Toast.makeText(this, "Hello, views!", LENGTH_SHORT).show()
    }
以上代码表示点击“hello”按钮后的事件响应。 然而在 Kotlin 中使用 lambda 表达式会让代码更加简洁清晰：

    hello.setOnClickListener {
        toast("Hello, views!")
    }
Anko 库默认提供 toast 函数。

详情请参阅[kotlin@kotliner.cn](https://www.kotlincn.net/docs/tutorials/kotlin-android.html)
## 语法基础
#### 1. 基础语法
##### 定义包
包的声明处于源文件的顶部
	
    package com.dou.kotlin.learnkotlin
 kotlin源文件和包的结构和目录无需匹配，也就是说源文件可以放在任何文件目录下。
##### 定义函数
函数使用关键子fun，参数格式为 参数:类型
	
    //　java转kotlin自动生成的oncreate
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }
    
     // 参数:(Int, Int),无返回值函数
    fun printSum (a : Int, b : Int) : Unit {
        println("sum is " + (a + b))
    }
    
    // 参数:(Int, Int),无返回值函数,unit类型可以省略
    fun printSum (a : Int, b : Int) {
        println("sum is " + (a + b))
    }
    
    // 参数:(Int, Int), 返回:(Int)
    fun sum (a : Int, b : Int) : Int {
        return a + b
    }
  
    // 参数(Int, Int), 返回(Int)
    // 表达式作为函数体，返回类型自动推断：
    fun mutilply (a : Int, b : Int) = a * b
    
    // 参数(Int, Int), 返回(Int)
    fun mutilply (a : Int, b : Int) : Int = a * b
    
    
##### 注释
###### 行注释

	// 这是行注释 
###### 多行注释

	/*这是多行注释
    */
#### 2. 基本数据类型
##### 定义变量
###### 定义一次赋值的变量
定义一次赋值的变量使用val修饰

	val a : Int = 1 //立即赋值，定义变量类型为Int
    val b = 2 //立即赋值，变量类型自动判断为Int
    val c : Int //如果没有初始赋值必须定义变量类型
    c = 3 //　明确赋值
###### 定义可变变量

	var a : Int = 3
    var b = 3
    var c : Int //如果没有初始赋值必须定义变量类型
    c = 3
##### 使用字符串模板
字符串模板就是一小段代码表示一个字符串。字符串模板以$表示

	 var a = 1
     var b = "a is $a"
     
     a = 2
     var d = "${b.replace("is", "was")}, but now is $a"
#### 3. 条件控制
##### if条件表达式

	// 条件表达式
    fun method4 (a : Int, b : Int) : Int{
        if (a > b) {
            return a
        } else {
            return b
        }
    }

    // 条件表达式,将函数式作为函数体
    fun method5 (a: Int,b: Int) = if (a > b) a else b
    
    
##### when条件表达式
when相当于java中的switch语句。
它的简单形式是
	
    when (参数) {
    	条件　-> 语句　// 参数符合条件的情况
        else -> 语句 // 其它情况
    }
    或者
    when {
    	条件表达式 -> 语句　// 符合条件表达式的时候执行
        else -> 语句 //其它情况
    }
when将它的参数和所有分支条件顺序比较，知道某个分支满足条件。如果其它分支都不满足条件，就会执行else分支，如果when作为表达式使用，则必须指定else语句。
    

	fun method16 () {
        var a = 9;
        when {
            a in 1..10 -> println("$a is in 1-10")
            a == 1 -> println("$a == 1")

            else -> println("other")
        }

        when (a) {
            !in 1..10 -> println("$a is not in 1-10")
            1 ->  println("$a == 1")

            else -> println("other")
        }

        fun hasPrefix (a: Any) = when (a) {
            is String -> a.contains("prefix")

            else -> false
        }
    }
#### 4.　循环控制
##### for循环	
for循环可以对任何提供迭代器的对象进行遍历,基本语法为

	for (a: Int in arrays) {
    	// ...
    }
对集合进行遍历
	
    var items = listOf<String>("kotlin","java", "c", "c++");
	for (i in items) {
    	println(i)
    }
    
    for (i in items.indices) {
        print(items[i])
    }
##### while循环
while循环基本的语法为

	while(布尔表达式) {
    	// 语句
    }
   
   	do {
    	// 执行语句
    } while (布尔表达式)
    
##### 跳转和返回
kotlin中跳转和返回与java类似,有break,return,continue。
kotlin中所有表达式都可以用标签标示。标签格式为	标识符后加@符号，例如abc@,mark@,要为一个表达式添加标签，需要在这个表达式前加上标签即可。

	loop@ for (i in 1..10) {
    }
标签可以配合continue和break使用。当有多层嵌套时，可以指定跳出到哪个循环体.

	outer@ for (i in 1..20) {
    	inner@ for (j in 1..20) {
        	if (...) break@outer
        }
    }
#### 5. 其它
##### 使用可空值和空值检测
当某个变量可以为空时，必须在变量类型声明后使用?表示该变量可以为空

    fun method6 (a : String?) {
        if (a == null) {
            println("ａ为空")
        } else{
            println(a)
        }
    }
    
kotlin的空安全设计对于声明可能为空的参数，在使用时要做判空处理，一般有两种处理方式：
1. 变量后加!!表示为空时抛出异常
2. 变量后加?表示不作处理返回null或者配合?:做空判断处理


	// 后边加?表示变量可能为空
    var a : String? = null

	//  变量后加!!表示抛出异常
    var b = a!!.toInt()

	// 变量后加?表示返回null时不做处理
    var c = a?.toInt()

	// ?配合?!做空判断处理
    var d = a.toInt() ?: -1
##### 类型检测
is运算符用来检测某个对象是否是某种类型的实例，相当于java中的instance of,在检测后，如果是某种类型的话，就可以直接当做该类型使用，不用显式的去转换

	if (obj is String) {
            return obj.length
    } else {
        return null
    }
##### 使用区间

##### 使用集合
#### 2. 习惯用法
#### 3. 编码习惯
## Kotlin基础

***笔记丢失了一部分之后再补上***

#### 数组

	
    fun method18(){
        var items = arrayOf(1,2,3,4) // 创建一个确定的数组[1,2,3,4]
        for (i in items) {
            print(i)
        }

        var array1 = arrayOfNulls<Int>(4) //　创建一个空数组
        for (i in array1.indices) {
            array1[i] = i　//　给数组元素赋值
        } 
    }
    
    // Array是一个工厂函数,　第一个参数指定数组的大小,第二个参数表达式是根据数组索引指定每个元素的值
    // 创建数组["1", "4", "9", "16"]
    var array2 = Array(4, {i -> (i * i).toString()})
    
#### 字符串
字符串用strng类型表示，字符串是不可变的。字符串的元素可以通过s[i]访问。也可以用for循环迭代字符串。

字符串分为原生字符串和转义字符串。

转义字符串类似java中的字符串，用""包含起来，可以使用转义字符。

原生字符串使用""" """包含起来，可以包括换行和任意文本。
默认|作为边界前缀。

	fun method19 () {
        var str = """
            |这是原生字符串
            |这是原生字符串
            |这是原生字符串
        """ 
        //  输出              |这是原生字符串    
        //  输出      		|这是原生字符串    
        //  输出   		    |这是原生字符串

        var strTrim = """
            |这是原生字符串
            |这是原生字符串
            |这是原生字符串
        """.trimMargin() // 输出　这是原生字符串
        						　这是原生字符串
                                  这是原生字符串

        var strCode = """
            for (i in 1..4) {
                print(i)
            }"""


        println(str)
        println(strTrim)
        println(strCode)
    }
## 类和对象
#### 类
kotlin中也是用关键字class声明类。
类声明由类名，类头(指定类继承，类构造方法等)和大括号包起来的类体组成，如果一个雷没有类体可以省略大括号。

类中可以有一个主构造方法和多个次构造方法，主构造方法是类头的一部分，跟在类名之后的，用constructor关键字修饰。如果主构造函数没有任何注解或者修饰可以省略。

	class Person constructor(name: String)
    
    class persion(name: String)
主构造函数不能包含任何代码。类的初始化操作可以放在类中init关键字的代码块中。

	class Persion(name: String, id: Long) {

        var pName: String = name
        var pId: Long = id

        init {
            // ...
        }
	}

主构造方法可以直接声明属性。
	
    class Persion(var name: String, var id: Long) {
        init {
            // ...
        }
	}


次构造方法的格式为
	
    constructor(参数: 参数类型) : this(...参数)
    
this(...参数)指代的是其它构造方法。如果这个类中没有主构造方法是可以省略的。如果类中有构造方法则这个类必须直接或者间接的委托主构造方法。委托到另一个构造方法用this关键字即可。

如果一个非抽象类没有声明任何构造方法，那么这个类会默认一个public的无参的主构造方法。

要创建类的实例不需要且没有java中的new关键字

类成员包括
* 函数
* 属性
* 嵌套类和内部类
* 对象声明

#### 属性
###### 属性声明
在类中可以用var修饰可变成员属性和val修饰只读成员属性。

要使用一个属性只用在对象后.该属性名即可。

	 var extendence = Extendence(1)
     print(extendence.name)
     
###### Getters和Setters
声明一个属性的完整语法是:

	var	<propertyName>[:	<PropertyType>]	[=	<property_initializer>]
				[<getter>]
				[<setter>]
                
初始化器,getter,setter方法都是可选的

	var	allByDefault:	Int?	//	错误:需要显式初始化器,隐含默认	getter	和	setter
    var	initialized	=	1	//	类型	Int、默认	getter	和	setter
#### 继承

要去声明一个显式的超类，在类头的最后加上:(超类名)(超类的构造方法)

	open class Base(p: Int)
    
    class Extendence(p: Int) : Base(p)
    
如果子类有主构造方法那么这个子类声明中必须要用子类的构造方法参数对父类进行初始化。如果子类没有主构造方法，那么在每个次构造方法必须使用super关键字初始化父类类型或者委托给其它的构造函数做到这一点。

	class Extendence : Base{
        constructor(p: Int) : super(p)
        constructor(p:Int, a: Int) : this(p)
	}
###### Any
Any是所有类的超类，但是Any类中只有三个方法,equals(),toString(),hashCode()。
###### fianl，open关键字
open代表该类可以被继承，类默认是final修饰的，也就是不能被继承。
###### 覆盖方法
kotlin需要显式的标注可被覆盖的成员和被覆盖的成员。
	
    open class Base(p: Int) {
    	open fun method(){}
	}
    
    class Extendence : Base{
        constructor(p: Int) : super(p)
        constructor(p:Int, a: Int) : this(p)

        override fun method(){}
    }
    
也就是说方法覆盖中父类和子类方法都要加修饰。父类的方法前要加open，子类方法要加override。如果父类方法没有用open修饰，子类不允许出现与父类相同签名的方法(同名同参同返回值)。

标记为override的成员本身也是开放的，可以被再次覆盖，如果想要禁止再次覆盖，需要使用final关键字。

	open class Entendence(p: Int) : Base(p){
    	final override fun method(){}
    }
###### 覆盖属性
覆盖属性的基本用法和覆盖方法类似。父类属性用open修饰，子类属性用override修饰。

你可以用一个var属性覆盖一个val属性，但是不能用val属性覆盖一个var.因为var属性本质上声明了一个getter和setting方法，val属性本质上只能声明getter方法。
###### 覆盖规则
kotlin支持多继承,如果一个子类继承多个父类且父类中存在同样的属性或者方法，那么这个子类必须覆盖这个方法。

	open class A(p: Int){
    	open fun f(){}
        fun v()}{
    }
    
    interface B{
    	fun f(){}
        fun v(){}
    }
    
    class C(p: Int) : A(P),B{
    	override fun f() {
        	super<A>.f();
            super<B>.f();
        }
        
        fun v(){}
    }
###### 抽象类
类或者类中的成员可以被声明为abstract,被声明为抽象的对象默认是开放的。

需要注意的是我们可以用抽象的成员去覆盖飞抽象成员。
###### 伴生对象
伴生对象的作用类似与java中的静态成员
#### 封装
#### 多态
#### 接口
#### 可见性修饰符
#### 数据类
#### 密封类
#### 泛型
#### 嵌套
#### 枚举
#### 对象
#### 委托
#### 委托属性
## 函数和lambda表达式

## 高级

## 其它

## 后记

## 参考