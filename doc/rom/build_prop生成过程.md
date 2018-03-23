## Android系统build.prop生成过程

1. 执行build/tools/buildinfo.sh
2. 将system.prop中的内容添加到$(OUT_TARGET_DEVICE)/system/build.prop
3. 将ADDITIONAL_BUILD_PROPERTIES的值也添加到$(OUT_TARGET_DEVICE)/system/build.prop

>注意： 对于b288，可以在device/$(TARGET_DEVICE)/$(TARGET_DEVICE).mk文件中修改配置文件来更改build.prop配置，但是如果直接make不会覆盖build.prop文件，需要删除build.prop文件或者make clean之后再make。