## virtualbox没有可用设备(不能识别usb)

> 记一次小坑，修改东西的时候要多想想有什么后果，不然以后遇到坑的时候根本无从下手。

在学linux用户管理的时候手贱改了一下主用户的用户组，当时也没想那么多，也忘记安装virtualbox及设置vm usb设备时添加用户组的操作，这次也是过了很久才又来刷系统，导致出现这个问题的时候一脸懵逼。

    1 sudo usermod -a -G vboxusers $(USER_NAME)
    2 shutdown -r now