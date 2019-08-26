# Ubuntu上下载百度文的大文件

## 思路
通过chrom插件获取到下载链接，再通过aria2下载该文件

## 准备
- [百度云chrome插件](https://github.com/acgotaku/BaiduExporter/blob/master/BaiduExporter.crx)

	具体使用[BaiduExporter](https://github.com/acgotaku/BaiduExporter)

- [aria2懒人包](https://github.com/aria2/aria2)

	[aria2c使用介绍](http://aria2c.com/usage.html),或直接使用sudo apt-get install aria2安装

## 步骤	

1. 下载chrome插件，并安装

2. 安装aria2

3. 打开aria2 

'''
aria2 --enable-rpc=true --check-certificate=false
'''
