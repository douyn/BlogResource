## Android系统build.prop生成过程

1. 执行build/tools/buildinfo.sh
2. 将system.prop中的内容添加到$(OUT_TARGET_DEVICE)/system/build.prop
3. 将ADDITIONAL_BUILD_PROPERTIES的值也添加到$(OUT_TARGET_DEVICE)/system/build.prop

>注意： 对于b288，可以在device/$(TARGET_DEVICE)/$(TARGET_DEVICE).mk文件中修改配置文件来更改build.prop配置，但是如果直接make不会覆盖build.prop文件，需要删除build.prop文件或者make clean之后再make。

可以直接使用adb对build.prop进行修改，步骤如下：

1. adb pull将system/build.prop拷贝出来以备修改。
2. 修改build.prop中配置文件的属性。例如,ro.sf.lcd_density=167修改为ro.sf.lcd_density=160.
3. adb push将文件push到system/build.prop
4. 设置权限。adb remount， adb shell chmod 0644 system/build.prop给文件设置权限。
