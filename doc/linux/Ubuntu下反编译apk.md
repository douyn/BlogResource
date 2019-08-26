## 在ubuntu下反编译apk

### 工具

1. apktool [https://github.com/iBotPeaches/Apktool](https://github.com/iBotPeaches/Apktool)
2. dex2jar [https://github.com/pxb1988/dex2jar](https://github.com/pxb1988/dex2jar)
3. jd-gui [https://github.com/java-decompiler/jd-gui](https://github.com/java-decompiler/jd-gui)

### 流程

大致的流程为:
1. apktool反编译得到资源文件,将apk文件改为.zip然后解压可以得到dex文件 
2. 使用dex2jar将dex文件转为jar
3. 使用jd-gui打开jar包


#### 获取dex文件	
##### 普通apk
直接将apk文件改为zip格式解压出来即可得到classes.dex
##### 系统apk
系统apk中源码和资源文件是分开的，例如PinyinIME拼音输入法，在/system/app/PinyinIME/下，包括PinyinIME.apk,oat,libs.资源是放在apk中，源码是放在odex中。所以如果要的到dex文件，首先要将odex转为dex

###### odex 转 smali
	java -jar libxx.jar x xxx.odex // 此时将在同级目录生成out目录，里边是各种smali文件
###### smali 转 dex
	java -jar smali.jar as out/ // 该指令是将out目录的文件重新打包成dex或者jar

#### 将dex文件转为jar
	./d2j-dex2jar.sh out.dex
#### 打开jar文件
	拖进jd-gui
### 参考
