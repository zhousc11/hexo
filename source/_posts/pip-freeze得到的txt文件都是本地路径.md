---
title: pip freeze得到的txt文件都是本地路径
tags:
  - 乱折腾
  - python
  - pip
  - 踩坑
  - 问题
excerpt: >-
  # 问题

  在conda中使用命令pip freeze >
  requirements.txt命令导出已安装的模块方便复制环境，其中部分模块显示为版本号，而有其他部分显示为了文件路径，例如cffi @file://……

  这样的依赖包在其他的设备上肯定不会...
date: 2023-09-23 16:35:00
categories:
---

\# 问题 在conda中使用命令pip freeze > requirements.txt命令导出已安装的模块方便复制环境，其中部分模块显示为版本号，而有其他部分显示为了文件路径，例如cffi @file://…… 这样的依赖包在其他的设备上肯定不会...
<!-- more -->
\# 问题

在conda中使用命令`pip freeze > requirements.txt`命令导出已安装的模块方便复制环境，其中部分模块显示为版本号，而有其他部分显示为了文件路径，例如`cffi @file://……`

这样的依赖包在其他的设备上肯定不会正常安装的，毕竟根本没有正确的版本号

# 解决

使用这个命令

```
pip list --format=freeze > requirements.txt
```

# 注意

使用这种方式导出的文件中会包含`distribute`,`pip`,`wheel`,`setuptools`，一定要手动删除！