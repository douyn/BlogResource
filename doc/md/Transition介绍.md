# Android New API - Transition (用法)

## 前言
4.4已经引入了Transition，5.0实现Transition
## 什么是Transition Framework

Transition Framework的核心是根据场景的不同帮助开发者自动生成动画。

## 为什么要用Transition

## 基本API和用法
开启Transition

* TransitionManager.go()
* beginDelayTransition()
* setEnterTransition()/setSharedElementEnterTransition()

#### scene

>A scene represents the collection of values that various properties in the View hierarchy will have when the scene is applied. A Scene can be configured to automatically run a Transition when it is applied, which will animate the various property changes that take place during the scene change.

场景。执行动画的View。通常由Scene.getSceneForLayout(sceneRoot, R.layout.basic_scene_a, this)来获取Scene实例。
sceneRoot是动画所在的viewgroup，layoutId是这个scene的View resouce。

#### TransitionManager.go()

通常事存在几个不同的scene,使用TransitionManager.go(sceneX);来切换scene；

#### beginDelayTransition()

#### setEnterTransition()


### 

### scale/translate动画	
	TransitionManager.beginDelayedTransition(scene_root);

                ViewGroup.LayoutParams params = view_change.getLayoutParams();
                if (sizeChanged) {
                    params.width = 100;
                    params.height = 100;
                } else {
                    params.width = 200;
                    params.height = 200;
                }
                sizeChanged = !sizeChanged;
                view_change.setLayoutParams(params);

## 应用

### 转场动画

### 元素共享

### 水波纹扩散效果