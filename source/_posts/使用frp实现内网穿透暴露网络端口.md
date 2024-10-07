---
title: 使用frp实现内网穿透暴露网络端口
tags:
  - 乱折腾
  - 远程桌面
  - Linux
  - 公网IP
  - 内网穿透
excerpt: >-
  上大学了，我购买的电脑是MacBook Air，使用了macOS系统，而学校开设的课程中，有许多例如数据库技术这样的课程，需要使用到像是Microsoft
  SQL Server这样的Windows下独占的软件，而macOS端没有相应的软件，为了完成学习作业...
date: 2023-03-13 23:25:00
categories:
---

上大学了，我购买的电脑是MacBook Air，使用了macOS系统，而学校开设的课程中，有许多例如数据库技术这样的课程，需要使用到像是Microsoft SQL Server这样的Windows下独占的软件，而macOS端没有相应的软件，为了完成学习作业...
<!-- more -->
上大学了，我购买的电脑是MacBook Air，使用了macOS系统，而学校开设的课程中，有许多例如数据库技术这样的课程，需要使用到像是Microsoft SQL Server这样的Windows下独占的软件，而macOS端没有相应的软件，为了完成学习作业任务，我想到了使用远程桌面连接家里的电脑来完成作业。

使用类似于Todesk这样的软件，确实可以很方便的进行远程控制，但是体验却不是很好，第三方App中存在很多广告、分辨率不能调整，不能适配Mac上的高分屏等小小的问题让我下定了决心使用Microsoft RDP进行远程连接。

然而，因为IPv4地址枯竭问题，中国大陆的路由器的IPv4地址都是经过运营商NAT转换后的地址，不可以直接远程连接，这样以来就需要内网穿透，将处于运营商NAT内网中的计算机端口暴露出给公网，从而可以进行远程连接，这时候就需要内网穿透了。

# 环境

*   Windows 10 22H2(19045.2673)
*   阿里云ECS\_2c2g1m，CentOS7.9

# 工具

我使用的内网穿透软件是[`frp`](https://github.com/fatedier/frp)，这个工具很多平台都可以使用，而且配置也很简单，所以我很喜欢这个工具，另外你还需要一台有公网IP的PC/服务器，我用的是阿里云的ECS

# 服务端配置

## 下载服务端

先ssh登录到服务器，去releases页面下载frp的服务端

```
wget https://github.com/fatedier/frp/releases/download/v0.48.0/frp_0.48.0_linux_amd64.tar.gz
```

解压

```
tar -zxvf frp_0.48.0_linux_amd64.tar.gz
```

将文件夹改名并进入文件夹

```
mv frp_0.48.0_linux_amd64.tar.gz frps && cd frps
```

## 写入配置文件

```
vi frps.ini
```

然后按一下`Insert`键或者`I`键，进入编辑模式

> 这是frps的配置文件，可以根据自己的需求来修改 以下是我的配置文件

```
[common]
bind_port = 6998 # 服务端的绑定端口，客户端和服务器使用这个端口通信
kcp_bind_port = 6998 # 如果使用kcp协议，就需要写这个，可以和绑定端口一致，不用kcp就不用写
dashboard_port = 7000 # 管理页面的端口，设置后可以使用IP:端口查看服务状态
dashboard_user = xxx # 管理页面的用户名
dashboard_pwd = xxxxxx # 管理页面的密码
log_file = ./frps.log # frps的日志文件绝对路径
log_level = info # 日志等级
log_max_days = 3 # 最长日志保留时间
```

写完后，按一下`ESC`，然后输入`:wq`保存就可以了

回到终端，输入

```
./frps -c frps.ini
```

试运行一下，如果显示这个  
![](https://zhoushicheng.cn/legacy_imgs/2554738332.png) 并没有报错，就代表已经成功了，可以按`Control+C`结束试运行了

## 配置系统守护进程

下载`systemd`

```
yum -y install systemd
```

创建服务

```
vim /etc/systemd/system/frps.service
```

写入内容

```
[Unit]
# 服务名称，可自定义
Description = frp server
After = network.target syslog.target
Wants = network.target

[Service]
Type = simple
# 启动frps的命令，需修改为您的frps的安装路径
ExecStart = /path/to/frps -c /path/to/frps.ini

[Install]
WantedBy = multi-user.target
```

使用`systemd`命令，管理服务

```
systemctl start frps
systemctl stop frps
systemctl restart frps
systemctl status frps
```

配置frps开机自启

```
systemctl enable frps
```

至此，服务端配置完成

# 客户端配置

## 下载并解压客户端文件

在GitHub的[releases页面](https://github.com/fatedier/frp/releases/tag/v0.48.0)下载程序文件

## 编辑配置文件

使用文本编辑器打开`frpc.ini`文件，编辑内容

> 以下是我的配置文件
> 
> ```
> 
> [common]
> server_addr = 0.0.0.0 # 你的服务器的公网IP地址，用于通信
> server_port = 6998 # 前面的绑定地址，用于和你的服务器通信的端口
> protocol = kcp # 使用协议，如果前面没有kcp端口就不用写
> log_file = ./frpc.log # 日志文件的路径
> log_level = info # 日志级别
> log_max_days = 3 # 日志保留时长
> ```

# 上面的是通用的，一些关于服务器的基本的内容

# 下面的是你需要暴露的服务和端口信息

\[rdp\] # 方括号中间的是你的服务名，随便写，认得出来就行 type = tcp # 是TCP还是UDP协议 local\_ip = 127.0.0.1 # 要暴露的本地IP local\_port = 3389 # 要暴露的本地端口 remote\_port = 6000 # 属于这个服务的远程端口，你连接就靠这个端口 use\_compression = true # 是否压缩，可以提升性能，但是可能加重服务器负担

\[rdp\_udp\] type = udp local\_ip = 127.0.0.1 local\_port = 3389 remote\_port = 6000 use\_compression = true

```
根据业务需求填写完成后就可以保存了
## 启动服务
按住`shift`鼠标右键，点“在此处打开Powershell窗口”
![](https://zhoushicheng.cn/legacy_imgs/1032484514.png)
输入
```

.\\frpc.exe -c frpc.ini

```

如果没有报错在输出的内容里就会有暴露的端口名，这样内网穿透服务就被搭建完了  

# 连接到隧道
以Microsoft RDP服务为例  

使用`服务器公网IP+remote_port`连接到隧道  

报证书错误是正常的
![](https://zhoushicheng.cn/legacy_imgs/1087770764.png)
这样就成功了  

# 后记
你可能需要将Windows上的frpc封装为服务，请参阅此文章
```