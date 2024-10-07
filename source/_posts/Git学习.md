---
title: Git学习
tags:
  - 学习日记
  - git
  - ops
  - study
excerpt: >-
  最近一直都在持续学习git相关内容，越来越发现git是一个十分适合大型项目和团队协作进行开发的工具，掌握好了对于我们参与项目维护和开发产品帮助很大，所以要不断持续学习git。

  tags & releases

  tag的创建

  当我们在git版本控制中遇...
date: 2024-04-01 19:33:41
categories:
---

最近一直都在持续学习git相关内容，越来越发现git是一个十分适合大型项目和团队协作进行开发的工具，掌握好了对于我们参与项目维护和开发产品帮助很大，所以要不断持续学习git。 tags & releases tag的创建 当我们在git版本控制中遇...
<!-- more -->
最近一直都在持续学习git相关内容，越来越发现git是一个十分适合大型项目和团队协作进行开发的工具，掌握好了对于我们参与项目维护和开发产品帮助很大，所以要不断持续学习git。

# tags & releases

## tag的创建

当我们在git版本控制中遇到了一些里程碑的commit，例如当我们的产品主要功能开发完成，可以交付给客户的时候，我们就可以创建一个tag来作为一个大版本标记，我们就可以在以后的某个时刻，快速定位一个开发阶段，方便我们创建一个branch或者checkout。

例如下面这张图，我们就可以在pycharm中选择一个commit，然后创建一个tag进行标记。

![2024-04-01T11:21:43.png](https://zhoushicheng.cn/legacy_imgs/105357483.png)

> 但是需要注意的是，我们在ide中创建了tag后，在我们push到远程仓库的时候，ide并不会默认将你创建的tag推送到远程仓库。

所以我们就需要去在push的界面将这个tag push到远程仓库去。例如pycharm中：

![2024-04-01T11:25:50.png](https://zhoushicheng.cn/legacy_imgs/3644367200.png)

然后我们就可以在GitHub中看到这个tag。此时就是我们创建release的时候了。

## release的使用

刚刚说了tag一般是意味着我们的开发进程中具有一定成果或者里程碑的开发阶段，我们就很有可能将其作为一个发行版交付给用户，这时我们就需要创建一个release，表示是当前版本最终的发行版，是一个功能完整，逻辑清晰的状态，可供下载了。

此时我们就可以选择一个tag作为发行版，将其发布在我们的项目界面上。

![2024-04-01T11:28:54.png](https://zhoushicheng.cn/legacy_imgs/2559221582.png)

在这个页面我们就可以选择一个被push到远程仓库的tag作为发行版，然后在下面可以写你的release notes，上传你的编译好的二进制文件方便用户下载。

创建完成后就可以在GitHub页面看到你的发行版了。

![2024-04-01T11:30:07.png](https://zhoushicheng.cn/legacy_imgs/269738494.png)

![2024-04-01T11:30:22.png](https://zhoushicheng.cn/legacy_imgs/2820077679.png)

# 错误commit怎么办

在项目实际开发进程中，我们可能会遇到不小心错误的将更改commit，但是并未push到远程仓库，此时我们就可以使用一些方法修改我们上次的commit，将其drop掉。

不少ide都提供了这个后悔药，例如pycharm中

![2024-04-01T11:32:03.png](https://zhoushicheng.cn/legacy_imgs/1807717243.png)

这里的`drop commit`就是表示我们可以将这个commit丢弃掉，我们的提交历史就像是从未有过这个commit一样，我们就可以保证我们的commit历史干净整洁，方便我们进行后期追踪和查看。

## 原理