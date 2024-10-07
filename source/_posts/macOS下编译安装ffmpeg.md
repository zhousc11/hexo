---
title: macOS下编译安装ffmpeg
tags:
  - 小技巧
  - ffmpeg
  - 编译
  - macOS
  - 源代码
excerpt: >-
  最近想要在电脑上安装you-get这个工具，安装的时候发现官方建议要安装ffmpeg这个软件，于是就尝试安装。但是发现ffmpeg这个软件功能实在太强大了，有很多可选择的配置，于是决定下载源代码，自己编译安装，顺便学习一下编译安装的流程。 
   

  FFmp...
date: 2023-03-17 11:41:12
categories:
---

最近想要在电脑上安装you-get这个工具，安装的时候发现官方建议要安装ffmpeg这个软件，于是就尝试安装。但是发现ffmpeg这个软件功能实在太强大了，有很多可选择的配置，于是决定下载源代码，自己编译安装，顺便学习一下编译安装的流程。 FFmp...
<!-- more -->
最近想要在电脑上安装you-get这个工具，安装的时候发现官方建议要安装ffmpeg这个软件，于是就尝试安装。但是发现ffmpeg这个软件功能实在太强大了，有很多可选择的配置，于是决定下载源代码，自己编译安装，顺便学习一下编译安装的流程。

![](https://ffmpeg.org/favicon.ico)

> FFmpeg 是一个开放源代码的自由软件，可以执行音频和视频多种格式的录影、转换、串流功能，包含了libavcodec——这是一个用于多个项目中音频和视频的解码器库，以及libavformat——一个音频与视频格式转换库。

> 来自Wikipedia

# 先决条件

*   Xcode，可以在AppStore下载
*   已经安装HomeBrew，参考此文
*   互联网连接

# 下载源代码

打开终端，使用Git拉取源代码

```
git clone git://source.ffmpeg.org/ffmpeg.git
```

等待拉取完成  
![](https://zhoushicheng.cn/legacy_imgs/312137519.png) 进入目录

```
cd ffmpeg
```

查看可用的版本并选择

```
git branch -a
# 我选择6.0
git checkout remotes/origin/release/6.0
```

![](https://zhoushicheng.cn/legacy_imgs/256454407.png)

# 编译源代码

继续执行以下命令

```
./configure --prefix=/usr/local/ffmpeg \
--enable-gpl \
--enable-version3 \
--enable-nonfree \
--enable-postproc \
--enable-libass \
--enable-libcelt \
--enable-libfdk-aac \
--enable-libfreetype \
--enable-libmp3lame \
--enable-libopencore-amrnb \
--enable-libopencore-amrwb \
--enable-libopenjpeg \
--enable-openssl \
--enable-libopus \
--enable-libspeex \
--enable-libtheora \
--enable-libvorbis \
--enable-libvpx \
--enable-libx264 \
--enable-libxvid \
--disable-static \
--enable-shared
```

其中的`--prefix=/usr/local/ffmpeg`表示的是编译后的库文件存放地方，可以选择自己记得住或者喜欢的路径。

执行此命令时候，或许会遇到红色字体报错，例如  
![](https://zhoushicheng.cn/legacy_imgs/2993696943.png) 这时候就是你的依赖少了，直接使用`brew install <你缺少的依赖>`就可以了，然后最好重启一下终端，让环境变量生效。

如果电脑比较干净，可能缺少的依赖很多很多，一个一个安装就好了。

执行完没有报错后就可以开始编译了，输入

```
make &&sudo make install
```

等待编译完成就好了。

# 配置环境变量

根据刚刚在`./configure`设置的安装路径，设置环境变量

```
touch .bash_profile
vim .bash_profile
```

然后输入以下内容

```
export FFMPEG_HOME=/usr/local/ffmpeg
export PATH=$FFMPEG_HOME/bin:$PATH
```

即可。