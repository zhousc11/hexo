---
title: 将exe可执行文件封装为服务
tags:
  - 小技巧
  - 内网穿透
  - frp
  - Windows
  - 后台服务
excerpt: >-
  前两天在服务器和本地客户端上成功部署了内网穿透服务frp，然而运行客户端和客户端需要打开cmd或者powershell输入命令，一旦关闭了cmd窗口或者powershell，进程就会被杀死，服务不能运行了，所以我们需要将exe程序封装为Windows的系统...
date: 2023-03-14 20:17:00
categories:
---

前两天在服务器和本地客户端上成功部署了内网穿透服务frp，然而运行客户端和客户端需要打开cmd或者powershell输入命令，一旦关闭了cmd窗口或者powershell，进程就会被杀死，服务不能运行了，所以我们需要将exe程序封装为Windows的系统...
<!-- more -->
前两天在服务器和本地客户端上成功部署了内网穿透服务frp，然而运行客户端和客户端需要打开cmd或者powershell输入命令，一旦关闭了cmd窗口或者powershell，进程就会被杀死，服务不能运行了，所以我们需要将exe程序封装为Windows的系统服务，就像是Linux上的systemd一样，在用户尚未登录时就可以运行，完美实现无人值守。

# 环境配置

*   Windows 10 22H2(19045.2673)

# 需要准备的

*   [instsrv和srvany工具（点击下载）](https://dl.zhoushicheng.cn/d/%E5%B8%B8%E7%94%A8%E8%BD%AF%E4%BB%B6/instsrv%2Bsrvany.rar)
*   你要封装为服务的exe文件

# 开始注册为服务

## 安装工具

将`instsrv`和`srvany`这两个工具移动到`C:\Windows\SysWoW64`和`C:\Windows\System32`这两个目录，方便我们后期进行调用工具

## 安装服务

打开cmd窗口，并以管理员身份运行，输入命令

```
instsrv frpc C:\WINDOWS\SysWOW64\srvany.exe
```

如果提示`The service was successfully added!`这样的字样，就表示你的服务已经成功添加了，然而需要注意的是这里注册的这个frpc的服务只是一个皮，我们还需要后续在注册表内进行配置，将这个服务指向我们需要执行的可执行文件

## 配置注册表

使用快捷键`Win+R`，打开运行窗口，输入`regedit`并回车，打开注册表编辑器

依次展开注册表，到这个目录下

`计算机\HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\`

在左边寻找自己创建的服务名，例如我是frpc的服务，只需要找到frpc这一项

![](https://zhoushicheng.cn/legacy_imgs/3682712000.png) 然后右键，新建项

![](https://zhoushicheng.cn/legacy_imgs/2022280833.png)

把这一项改名为`Parameters`

然后单击`Parameters`这个项，在右边的窗口里新建几个字符串值

![](https://zhoushicheng.cn/legacy_imgs/1300476002.png)

> *   `AppDirectory`这个项，是你要运行的exe文件的目录
> *   `Application`这个项，是你要运行的exe文件的具体路径
> *   `AppParameters`这个项，是你要运行exe文件的参数，可以为空

例如我的frpc服务的配置

![](https://zhoushicheng.cn/legacy_imgs/942060673.png)

写完这几项后，就完成了对这个服务的配置了

## 启动服务

使用快捷键`Win+R`打开运行窗口，输入`services.msc`打开服务窗口，在列表中找到你刚刚创建的服务名，启动即可

![](https://zhoushicheng.cn/legacy_imgs/4204688910.png)

# 注意

如果最后一步启动服务提示失败，大概率是因为注册表中的那三项配置有问题，建议重新检查一下