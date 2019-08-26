## 将独立应用作为library module导入项目

可以视为插件化思想,之后可以作为插件方便调用.

1. File-New-Import Module
2. 修改module build.gradle.主要修改: 1. 将apply plugin: 'com.android.application'改为'com.android.library' . 2. 将applicationId删除 3. 如果有releaseConfigs 删除之.
3. 修改Module AndroidManifest.xml. 主要修改: 将application标签中的icon leble等去掉
4. 代码修改. 1. library module中不能使用switch case,android studio alt+enter 自动修复代码可以自动转换 2. 如果module中使用了butterkenife,可参考butterknife readme中步骤将R替换为R2. 3. 项目中可能使用了系统数据库,需要修改系统数据库path,也可以改为直接使用getapplicationcontext().getdatabasepath(databsename).getabsolutepath();