star法则
situaction ：介绍项目的背景
task：介绍自己的职责
action：介绍自己所做的工作
result：介绍自己的成果

s：Android电子书阅读器
t: launcher界面和阅读器模块
a: 
1. ndk(编译mupdf/koreader等开源项目so，进行深度开发)
2. 适配(1072*1440与普通手机)(因为是e-ink屏幕所以减少刷新频率，使用一些少刷新的控件)
3. 自定义view实现阅读器(pageview)及部分ui(搜索view，亮度调节view，开机动画)
4. 自定义recyclerview(可以横向滑动的recyclerview)
5. 封装网络接口显示ui数据(没有给出接口的时候，事先封装好databean)
6. 使用greendao实现本地数据库接口(launcher的书架数据库和阅读器的书签，笔记，书摘数据库)
7. 其它(仿京东购买详情页，本地图书管理，第三方登录，第三方支付)


r: 成果及收获

1. 完成所有ui以及大部分功能的开发(mupdf是一个完整的开源项目，并不是只针对Android的开源项目，所以需要自己去编译相关的ndk库，c层的东西是有很多忘记了，所以大多数功能是在java层做的，例如添加菜单，触摸事件控制，书签笔记等，因为demo是很简单的，有的问题例如修改排版和字体需要在c层实现)
2. 对ndk有了系统实际的开发经验，对make fike及aosp有一定认识(aosp编译失败)
3. 对阅读器行业有一定认识
4. 对自身的优缺点有进一步认识(强[ui，自定义view]，弱[底层，阅读代码能力，缺乏性能优化实践])
5. 业余实践搞了一下ffmpeg和基于ijkplayer的视频播放器。看了一些kotlin和python的语法
6. 从入门到放弃xxxx