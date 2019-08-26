## ubuntu下使用charles抓包
#### 1. 下载安装charles
    1. wget -q -O - https://www.charlesproxy.com/packages/apt/PublicKey | sudo apt-key add -
    2. sudo apt-key adv --keyserver pgp.mit.edu --recv-keys 1AD28806
    3. sudo sh -c 'echo deb https://www.charlesproxy.com/packages/apt/ charles-proxy main > /etc/apt/sources.list.d/charles.list'
    4. sudo apt-get update
    5. sudo apt-get install charles-proxy

也可以直接安装体验版。
#### 2. 设置
##### 1. 代理设置
proxy -> proxy settings，勾选Enable transparent HTTP proxying，默认端口8888就可以
##### 2. 抓包设置(可选)
edit -> preference -> viewers，取消勾选combine request and response。
#### 3. 手机抓包
charles也可以进行电脑抓包，上述设置完就可以了。

确保手机所链接的wifi和电脑wifi在同一局域网下。

手机wifi设置代理，主机为电脑ip，端口为8888.

设置完以后，手机打开任意网页看是否能够打开，如果不能，请重新检查上述步骤。如果可以的话，charles界面上也会显示url数据了。
#### 4. https
charles抓包打开https数据中有unknown，并且返回的数据中有乱码。这是因为没有设置https。

在上述步骤的基础上，在手机端打开http://chls.pro/ssl/ + ip + 端口。一般手机会提示下载安装证书，如果安装成功就可以进行https抓包了。

对小米手机，使用uc浏览器打开上边的http://chls.pro/ssl/ + ip + 端口下载pem证书.打开设置->更多设置->系统安全->从sd卡安装凭证，注意这里默认可能没有显示你下载的证书，点击左上角的按钮，不要选择最近或者下载内容，选择你的设备，然后出来文件系统，找到你下载的证书文件，点击即可安装。
#### 5. 破解
最新版v4.2.0破解文件下载:
链接: https://pan.baidu.com/s/1sl4YMK1 密码: b8cy

将下载的charles.jar覆盖到/usr/lib/charles-proxy文件夹下即可。
#### 参考
[ [原创破解] charles-proxy 抓包工具 v4.2 Win + Mac + Linux全平台破解文件 ](https://www.52pojie.cn/thread-619994-1-1.html)

## 新增(2019-04-18 14:53:05)
#### Android 7.0以上抓包失败

Android7.0 之后默认不信任用户添加到系统的CA证书：

> To provide a more consistent and more secure experience across the Android ecosystem, beginning with Android Nougat, compatible devices trust only the standardized system CAs maintained in AOSP.（[文档链接](https://android-developers.googleblog.com/2016/07/changes-to-trusted-certificate.html)）

也就是说对基于 SDK24 及以上的APP来说，即使你在手机上安装了抓包工具的证书也无法抓取 https 请求 


### 解决方案
将抓包软件的证书安装成系统证书（需ROOT）









#### 参考
[Android 7.0 之后抓包 unknown 和证书无效的解决方案（无需改代码）](https://blog.csdn.net/ShadowySpirits/article/details/79756274)
