# Android性能优化

## 前言
有的时候做出来一个app很容易，但是做好一个app就需要很多方面的考虑。
## 代码优化
代码中可以优化的地方？

## 布局优化
为什么会卡顿？

Android系统每16ms都会发出VSYNC信号触发对UI的渲染，这就意味着大多数的操作要在16s内完成。如果由于某些原因受到VSYNC信号时，没有完成UI渲染，就会出现卡顿现象。

造成卡顿的操作？

* UI线程的耗时操作
* 多重嵌套布局，View过度绘制

如何检测卡顿操作？

* 使用HierarchyViewer查看布局是否过于复杂
* 在开发者选项中打开Debug GPU overdraw
* 使用TraceView观察CPU使用情况

如何进行优化？

* 减少布局的层级，减少使用include，ViewStub等
* onDraw()中尽量不做耗时操作，避免ui不刷新
* 使用recyclerview代替listview，或者在listview中使用viewholder和convertview

## 数据库优化

## 网络优化

## 内存优化

## 小结