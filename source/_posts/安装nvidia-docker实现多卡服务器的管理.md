---
title: 安装nvidia-docker实现多卡服务器的管理
tags:
  - 小技巧
  - Linux
  - cuda
  - nvidia
  - dl
  - gpu
  - docker
  - ubuntu
excerpt: |-
  # 背景
  实验室同学买了台四卡V100机器搞深度学习，需要方便的进行环境管理
  步骤

  安装docker
  安装Nvidia docker
  拉取镜像测试
  制作适用于自己的镜像
  运行镜像，优化一下（端口映射，远程ssh等等）
  ...
date: 2024-01-14 12:09:00
categories:
---

\# 背景 实验室同学买了台四卡V100机器搞深度学习，需要方便的进行环境管理 步骤 安装docker 安装Nvidia docker 拉取镜像测试 制作适用于自己的镜像 运行镜像，优化一下（端口映射，远程ssh等等） ...
<!-- more -->
\# 背景

实验室同学买了台四卡V100机器搞深度学习，需要方便的进行环境管理

# 步骤

1.  安装docker
2.  安装Nvidia docker
3.  拉取镜像测试
4.  制作适用于自己的镜像
5.  运行镜像，优化一下（端口映射，远程ssh等等）