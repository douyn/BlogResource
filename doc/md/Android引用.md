## Android 引用类型

## GC机制

## 引用
引用分为四种，按级别顺序为:

强引用 > 软引用 > 弱引用 > 虚引用
### 强引用
定义：当内存空间不足时，java虚拟机宁抛破除异常也不会回收的引用对象被称为强引用。一般情况下，内存中的对象只有当没有强引用指向它的时候，垃圾回收器才考虑对此内存进行垃圾回收操作。比如 obj = null时obj对象没有任何引用，将会被回收。

使用形式：
Android中大部分引用都是强引用，一般格式为
Object obj = New Object();这个obj对象就是一个强引用对象。

引用场景：

### 软引用
定义：

使用形式：
Object obj = new Object();
SoftReference<Object> softReference = new SoftReference<Object>(obj);
通过对象的强引用作为参数，创建一个软引用对象
应用场景：
### 弱引用
定义：

应用场景：

### 虚引用
定义：

应用场景：