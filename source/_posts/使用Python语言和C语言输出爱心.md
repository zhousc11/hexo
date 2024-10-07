---
title: 使用Python语言和C语言输出爱心
tags:
  - 乱折腾
  - C
  - python
  - leisure
excerpt: |-
  今天我们来写如何使用两种常见的编程语言（Python和C语言）输出一个爱心

  Python

  Python比较强大，内置了Turtle画图工具，我们可以很轻易的使用它画出一个真正的爱心

  安装Python
  下载这个文件，然后双击打开，直接把下面两个勾打上
  ...
date: 2024-01-26 19:02:00
categories:
---

今天我们来写如何使用两种常见的编程语言（Python和C语言）输出一个爱心 Python Python比较强大，内置了Turtle画图工具，我们可以很轻易的使用它画出一个真正的爱心 安装Python 下载这个文件，然后双击打开，直接把下面两个勾打上 ...
<!-- more -->
今天我们来写如何使用两种常见的编程语言（Python和C语言）输出一个爱心

* * *

# Python

> Python比较强大，内置了`Turtle`画图工具，我们可以很轻易的使用它画出一个真正的爱心

## 安装Python

下载[这个](http://118.31.170.151/python3.12.exe)文件，然后双击打开，直接把下面两个勾打上

![2024-01-26T12:14:54.png](https://zhoushicheng.cn/legacy_imgs/2031826579.png)

然后点上面的`Install Now`按钮就行，应该会弹出来一个UAC控制弹窗，点是就行，等进度条走完，然后点右下角的Close就安装完了Python了

## 打开IDLE

> `IDLE`是Python自带的一个写代码的编辑器，我们可以在那里面写代码，我们就在这里面写爱心代码

刚刚安装完了Python了，电脑里应该就会多出几个东西，可以在`开始菜单-所有程序`里面找到一个叫做`IDLE(Python 3.12 64-bit)`的软件了，比如下面的这个图里的第二个

![2024-01-26T12:22:37.png](https://zhoushicheng.cn/legacy_imgs/4218884467.png)

点开他就行

## 写代码

然后会出现一个这样的界面

![2024-01-26T12:23:26.png](https://zhoushicheng.cn/legacy_imgs/84400073.png)

然后点左上角的`File`，然后选择`New File`

![2024-01-26T12:30:35.png](https://zhoushicheng.cn/legacy_imgs/2609154853.png)

然后就出现一个空的框框，就可以在这里写代码了，来，下面是重头戏，我们的代码在这里

```python
import turtle

# 设置画布
screen = turtle.Screen()
screen.title("Turtle Heart")

# 创建一个画笔
pen = turtle.Turtle()
pen.color("red")
pen.begin_fill()

# 画爱心
pen.left(140)
pen.forward(224)
for _ in range(200):
    pen.right(1)
    pen.forward(2)
pen.left(120)
for _ in range(200):
    pen.right(1)
    pen.forward(2)
pen.forward(224)

pen.end_fill()
pen.hideturtle()

# 保持画布打开状态
screen.mainloop()
```

你可以复制或者自己手打代码写上去

就可以了

## 运行代码文件

点击上面的`Run`然后点`Run Module`

![2024-01-26T12:45:06.png](https://zhoushicheng.cn/legacy_imgs/2080924908.png)

点确定，选择一个位置保存你的文件，别忘了写下面的文件名，随便写都行，比如我就叫`test`

![2024-01-26T12:46:02.png](https://zhoushicheng.cn/legacy_imgs/1327386210.png)

然后点击保存，就会开始运行了，😂 ![2024-01-26T12:46:42.png](https://zhoushicheng.cn/legacy_imgs/432611655.png)

好了，这就是用Python画爱心的过程了

* * *

# C语言

> C语言也可以画爱心，但是C语言本来就不如Python那么强大，所以我们采用使用`*`这个符号来拼出一个爱心

## 安装Dev C++

点[这里](http://118.31.170.151/Devcpp.exe)，下载DevC++软件，然后双击打开，如果弹出UAC弹窗，点是给他权限就行

然后应该有一些让你选择语言啥的，默认英语就行，然后接下来保持默认直接狂点`Next`就行

![2024-01-26T12:52:02.png](https://zhoushicheng.cn/legacy_imgs/2027733979.png)

等待安装完成就行，安装完直接点Finish就行

第一次进去会让你选择语言，你可以选择你喜欢的语言，比如我们选择简体中文，然后一直点`Next`就可以进入软件了

![2024-01-26T12:53:26.png](https://zhoushicheng.cn/legacy_imgs/3285640233.png)

## 创建文件编写代码

然后依次点击`文件-新建-源代码`

![2024-01-26T12:54:15.png](https://zhoushicheng.cn/legacy_imgs/3878386047.png)

下面就有了一个写代码的环境，接下来是代码

```c
#include <stdio.h>

int main() {
    int i, j;
    int n = 10; // 可以调整心形的大小

    for (i = n/2; i <= n; i+=2) {
        // 打印左边的星号
        for (j = 1; j < n-i; j+=2) {
            printf(" ");
        }

        // 打印左半边的星号
        for (j = 1; j <= i; j++) {
            printf("*");
        }

        // 打印中间的空格
        for (j = 1; j <= n-i; j++) {
            printf(" ");
        }

        // 打印右半边的星号
        for (j = 1; j <= i; j++) {
            printf("*");
        }

        printf("\n");
    }

    // 打印心形的下半部分
    for (i = n; i >= 1; i--) {
        for (j = i; j < n; j++) {
            printf(" ");
        }

        // 打印下半部分的星号
        for (j = 1; j <= (i*2)-1; j++) {
            printf("*");
        }

        printf("\n");
    }

    return 0;
}
```

同样，你可以复制或者手打代码也行

> 注意一下，第五行代码有一个`int n = 10;`这个10是用来控制爱心的大小的，数字越大心越大，哈哈哈

## 运行代码

写完代码后，就可以依次点击`运行-编译运行`

![2024-01-26T12:56:28.png](https://zhoushicheng.cn/legacy_imgs/1279433517.png)

然后选择一个代码文件保存的地方，点击保存以后就可以了

![2024-01-26T12:57:07.png](https://zhoushicheng.cn/legacy_imgs/1222909212.png)

保存完了以后，应该就会出来了

![2024-01-26T12:58:08.png](https://zhoushicheng.cn/legacy_imgs/3922473996.png)