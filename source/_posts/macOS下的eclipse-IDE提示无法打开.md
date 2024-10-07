---
title: macOS下的eclipse IDE提示无法打开
tags:
  - 小技巧
  - macOS
  - eclipse
  - 错误
  - 签名
  - 权限
excerpt: >-
  前两天刚刚在Mac上安装了eclipse，想要写一个Java，但是却弹出一个错误窗口，提示“应用程序eclipse无法打开”。不知道是什么问题，在访达中的Application文件夹中右键打开也不能用，不知道是什么问题。  

  后来查找了很多资料，终于找到了...
date: 2023-03-19 11:29:29
categories:
---

前两天刚刚在Mac上安装了eclipse，想要写一个Java，但是却弹出一个错误窗口，提示“应用程序eclipse无法打开”。不知道是什么问题，在访达中的Application文件夹中右键打开也不能用，不知道是什么问题。 后来查找了很多资料，终于找到了...
<!-- more -->
前两天刚刚在Mac上安装了eclipse，想要写一个Java，但是却弹出一个错误窗口，提示“应用程序eclipse无法打开”。不知道是什么问题，在访达中的Application文件夹中右键打开也不能用，不知道是什么问题。

后来查找了很多资料，终于找到了问题所在，是因为权限不足的问题，所以无法打开。

# 解决方法

重新将这个App签一次名即可

```
sudo codesign --force --deep --sign - /Applications/Eclipse.app
```

输入完回车以后，会提示`/Applications/Eclipse.app: replacing existing signature`，这里耐心等待它执行完就可以打开了。

# 参考文档

[Reddit](https://www.reddit.com/r/setupapp/comments/ocfazs/how_to_fix_namespace_codesigning_code_0x1_error/)