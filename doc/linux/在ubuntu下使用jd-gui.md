## 在Ubuntu下使用jd-gui

### 说明
jd-gui是一个开源的java反编译软件
在github上的仓库是
[https://github.com/java-decompiler/jd-gui](https://github.com/java-decompiler/jd-gui)

### 1. 安装
jd-gui 1.5.2 下载地址: [https://github.com/java-decompiler/jd-gui/releases/download/v1.5.2/jd-gui-1.5.2.deb](https://github.com/java-decompiler/jd-gui/releases/download/v1.5.2/jd-gui-1.5.2.deb)

	wget https://github.com/java-decompiler/jd-gui/releases/download/v1.5.2/jd-gui-1.5.2.deb
	sudo dpkg -i ./jd-gui-1.5.2.deb

安装之后可以在/opt/jd-gui/下看到安装之后的文件 jd-gui.jar,使用java -jar /opt/jd-gui/jd-gui.jar或者在应用列表中都可以打开jd-gui

### 2. 使用
File->Open File 或者直接将文件拖入jd-gui


### 3. IDEA插件

1. 本地编译

	> git clone https://github.com/java-decompiler/jd-gui.git
	> cd jd-gui
	> ./gradlew build 
	> ./gradlew idea
	> java -classpath jd-gui.jar myextension1.jar;myextension2.jar org.jd.gui.App

2. 在线插件
File->Settings..->Plugins–>Browse repositores..搜索JD-Intellij

安装插件之后需重启
