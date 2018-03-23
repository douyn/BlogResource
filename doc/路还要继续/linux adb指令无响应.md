## linux adb指令无响应
现象是输入adb命令之后，终端无响应，既没有提示成功也没有提示失败。

运行 ps aux | grep adb,如果有输出 adb fork-server server，则说明adb正在运行，记下pid，执行kill $(pid)。

