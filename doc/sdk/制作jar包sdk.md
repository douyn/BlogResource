## 制作jar包sdk
1. 创建library项目或者设置原来项目为library.
	
    	apply plugin: 'com.android.library'
2. 编写sdk代码.
3. 再项目build.gradle里添加gradle task
	
    	task deleteOldJar(type:Delete){
            delete('build/libs/EinkUpdateHelper.jar')
        }

        task exportJar(type:Copy){
            from('build/intermediates/bundles/release/')
            into('build/libs/')
            include('classes.jar')
            rename('classes.jar', 'EinkUpdateHelper.jar')
        }

        exportJar.dependsOn(deleteOldJar,build)
        
4. 执行gradle task,在as右侧gradle标签卡,选择exportJar或者手动添加task.