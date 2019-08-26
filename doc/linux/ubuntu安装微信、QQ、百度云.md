# Ubuntu安装微信/QQ/百度云

## 相关
### Flatpak
意思就是一种构建框架，不同与Linux大多数应用安装需要依赖的特性，它把软件和依赖一起打包，相当于一个隔离的沙箱环境，可以很好的解决linux软件安装时的烦人的依赖问题。

### deepin-wine
deepin-wine就是一个平台，可以在其他平台运行windows程序

## 思路
原理就是通过flatpak框架把deepin下的wine平台移植到了其他平台上，借此来运行windows程序

## 步骤
1. 安装flatpak 

'''
sudo apt-get install flatpak
'''

2. 安装deepin-wine

下载地址：链接: [https://pan.baidu.com/s/1guT1k-YPhV3FVEp8PA4p5g](https://pan.baidu.com/s/1guT1k-YPhV3FVEp8PA4p5g) 密码: kvpd

下载之后安装
'''
flatpak install com.deepin.wine.Platform.2.18-10.flatpak
'''

3. 安装微信，其它应用也可以

TIM：https://www.lanzous.com/i1si94f

微信：https://www.lanzous.com/i1siabi

迅雷：https://www.lanzous.com/i1sic6f

百度云：https://www.lanzous.com/i26eamj

下载之后，安装对应.flatpak文件即可，命令为

'''
flatpak install xxx
'''

## 其它

#### 查看 已安装软件列表 

'''
flatpak list
'''

#### 命令行运行
通过flatpak list可以获取到软件id

然后再通过flatpak run id运行

'''
flatpak run com.tencent.wechat/i386/2.6
'''

推荐使用命令行方式启动，因为可以看到输出

如果遇到启动失败的情况,需要重新清除环境
'''
flatpak run ID -e
flatpak run ID -r
'''

## 存在的问题 
- 中文输入法不能用
- 微信不能接收文件
   
## 参考
[Ubuntu18.04 下使用Flatpak稳定安装TIM、微信、迅雷和百度云！](https://blog.csdn.net/u011469138/article/details/82320761)

