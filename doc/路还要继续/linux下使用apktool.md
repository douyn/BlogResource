## linux使用apktool
1. 下载apktool脚本文件。[apktool脚本下载地址](https://raw.githubusercontent.com/iBotPeaches/Apktool/master/scripts/windows/apktool.bat)，下载完最好直接改名为apktool
2. 下载apktool.jar文件,[apktool.jar下载任意release版本](https://bitbucket.org/iBotPeaches/apktool/downloads/)，下载完最好直接改名为apktool.jar
3. 将apktool脚本文件和apktool.jar文件移动到/usr/local/bin/目录下(此步骤需要root用户下操作)
4. 确保脚本文件名为apktool,jar包文件名为apktool.jar，如果不对请修改
5. 给这两个文件设置可执行权限，这里直接给所有权限了,chmod 777 apktool/chmod 777 apktool.jar 
5. 在终端使用apktool命令，验证是否成功

> 注意：
> 1. 注意最好在root下进行操作
> 2. 如果有如下报错:java.lang.UnsupportedClassVersionError: brut/apktool/Main，原因是java版本过低，请修改你的jdk版本。