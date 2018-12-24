## 搭建个人git服务器
工具
- ubuntu 18.04
- git 2.17.0
- ssh open-ssh

#### 创建git用户和目录

	# 创建git用户
    sudo useradd git
    # 设置git用户密码
    passwd git
    # 切换到git用户
    su git
    # 创建git用户目录,建议工作目录就放在这个git目录下
    mkdir git

#### 创建git项目

	# 切换到git用户
    su git
    # 进入工作目录
    cd ~/git
    # 创建git仓库
	git init --bare test.git
    
#### 添加ssh

	# 切换到git用户
    su git
    # 进入工作目录
	cd ~/git
    # 创建.ssh
    mkdir .ssh
    # 创建authorized_keys,并将客户端id_rsa.pub写入
    touch authorized_keys
    cat ${id_rsa.pub} > authorized_keys
    
#### 客户端测试

	git clone git@ip:/home/git/test.git
    
> 注意:
> 1. 如果不行可能是权限问题,
> 		chmod 0700 /home/git/.ssh
> 		chmod 0600 /home/git/.ssh/authorized_keys
> 2. 如果工作目录在别的用户目录下,记得修改目录owner
> 		chown -R git:git ${YOUR_WORK_ROOT}