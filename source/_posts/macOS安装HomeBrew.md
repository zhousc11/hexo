---
title: macOS安装HomeBrew
tags:
  - 小技巧
  - macOS
  - brew
  - homebrew
  - 包管理
excerpt: |-
  # 为什么要安装HomeBrew？
  HomeBrew是macOS/Linux上的一个缺失的包管理软件，我们可以通过这个工具去安装很多需要的软件、依赖等内容。
  例如安装wget
  brew install wget
  安装Firefox
  brew install...
date: 2023-03-20 23:03:00
categories:
---

\# 为什么要安装HomeBrew？ HomeBrew是macOS/Linux上的一个缺失的包管理软件，我们可以通过这个工具去安装很多需要的软件、依赖等内容。 例如安装wget brew install wget 安装Firefox brew install...
<!-- more -->
\# 为什么要安装HomeBrew？

HomeBrew是macOS/Linux上的一个缺失的包管理软件，我们可以通过这个工具去安装很多需要的软件、依赖等内容。 例如安装wget

```
brew install wget
```

安装Firefox

```
brew install --cask firefox
```

安装了brew，就感觉像是在Linux上的命令行一样，直接用一行命令去完成需要软件的安装，很方便。在我们进行软件开发的时候，经常也需要安装一些依赖，编译器等这样的内容，例如在安装ffmpeg的时候，就有七七八八的依赖需要安装；brew就可以轻松的帮助我们解决掉这些依赖项目。

# 安装方法

## 官方源

[官网](https://brew.sh/index_zh-cn)上的安装方式是直接在终端执行一行命令

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

确实是这样，安装HomeBrew只需要这一行代码就可以，

但是如你所见，这个脚本需要去连GitHub，在国内访问GitHub真是龟速，包括后面如果需要去下载什么配方包，也得去GitHub上下载，一直是几kb的速度，实在是很痛苦，所以在大陆使用中科大的源安装是一个好的注意

## 中科大源

先逐行执行以下四行命令，设置好环境变量

```
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.ustc.edu.cn/brew.git"
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.ustc.edu.cn/homebrew-core.git"
export HOMEBREW_BOTTLE_DOMAIN="https://mirrors.ustc.edu.cn/homebrew-bottles"
export HOMEBREW_API_DOMAIN="https://mirrors.ustc.edu.cn/homebrew-bottles/api"
```

然后使用官方安装脚本安装（因为已经设置好环境变量，不能访问的地址已经被替换）

```
/bin/bash -c "$(curl -fsSL https://github.com/Homebrew/install/raw/HEAD/install.sh)"
```

> 如果网络环境很差，差到连安装脚本都无法下载，可以使用以下脚本替代  
> `/bin/bash -c "$(curl -fsSL https://mirrors.ustc.edu.cn/misc/brew-install.sh)"`

等待命令完成，HomeBrew就安装到了你的机器上了。

# 切换为国内源

虽然已经将HomeBrew安装到了机器上，但是还是要修改镜像源为国内的，避免速度太慢。

执行以下命令

```
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.ustc.edu.cn/brew.git"
brew update
```

就可以了。这样一来安装什么软件都会从国内源下载，速度直接起飞～