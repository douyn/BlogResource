## Binder机制

### 1. 什么是Binder?
Binder是Android中跨进程通信的一种机制，基于C/S通信模型

### 2. Binder的原理
#### 2.1 Linux进程基础
- 进程隔离  进程和进程之间内存是不公用的，也就是说两个进程之间要进行数据传递就要用到跨进程通信
- 进程空间划分  用户空间和内核空间，目前操作系统都是使用的虚拟存储器，32位的系统寻址空间就是2^32也就是4G，3G用户空间，1G内核空间。
- 系统调用  用户空间只能通过系统调用进入内核空间

#### 2.2 传统的IPC通信原理
进程A 发送数据，然后通过系统调用copy_from_user()将数据拷贝到内核缓冲区，然后进程B 通过系统调用copy_to_user()将数据发送到进程B，进程B接收数据

#### 2.3 Binder跨进程通信原理
##### 2.3.1 动态内核可加载模块 && 内存映射
##### 2.3.2 Binder IPC实现原理

### 3. Binder通信模型
#### 3.1 Client/Server/ServiceManager/驱动
1. client/server/servicemanager存在于用户空间，通过open/ioctl/mmap操作内核空间
2. client/server需要用户自己实现，servicemanager和binder驱动是Android平台已经实现的

流程为
#### 3.2 Binder的线程管理
