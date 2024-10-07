---
title: 初识Git
tags:
  - 学习日记
  - 小技巧
  - git
  - github
  - ssh
  - http
  - vcs
  - pycharm
excerpt: |-
  # Git
  git是一个分布式版本控制软件，最初由林纳斯·托瓦兹创作，于2005年以GPL许可协议发布。最初目的是为了更好地管理Linux内核开发而设计。
  这么多年来一直都在用GitHub，但是仅限于clone和Download Zip & Rel...
date: 2024-01-13 21:56:00
categories:
---

\# Git git是一个分布式版本控制软件，最初由林纳斯·托瓦兹创作，于2005年以GPL许可协议发布。最初目的是为了更好地管理Linux内核开发而设计。 这么多年来一直都在用GitHub，但是仅限于clone和Download Zip & Rel...
<!-- more -->
\# Git

git是一个分布式版本控制软件，最初由林纳斯·托瓦兹创作，于2005年以GPL许可协议发布。最初目的是为了更好地管理Linux内核开发而设计。

这么多年来一直都在用GitHub，但是仅限于clone和Download Zip & Releases，从来没有真的用过git这个强大的工具，最近刚好在学习Python，急需可以**托管代码**，**同步代码**并且**追踪历史记录**的功能的工具，这时候我就想起了git好想有所有的这些功能，所以就开始用git来管理代码了

* * *

> 我写的代码全部都在本地，所以我下面的步骤仅仅是对于我这种情况而言的

今天搞了一下午，终于稍微搞懂了一点git，知道怎么用了，解决了我上面的那三个需求，步骤无外乎这几步：（我使用了这几步达到了我的目的）

> 1.  在git托管平台上创建一个仓库（我是在GitHub上创建的，当然还可以用gitlab啊，自建git服务等）
> 2.  在GitHub上创建token用于身份验证
> 3.  在本地创建（初始化）git仓库
> 4.  完成第一次commit & push，然后正常使用

大概就是这几步，其实配置起来也蛮简单

# 过程

## 创建一个仓库

打开GitHub，直接点右上角的加号，New Repository，输入你的仓库名，设置是否公开，然后不点以markdown初始化仓库就行

## 创建token用于身份验证

GitHub在2021年以后就禁止使用密码进行身份验证了，而是推荐使用PAT或者SSH Keys来进行身份验证，所以我们需要使用PAT或者SSH Keys进行验证。

这两种验证方式针对的是不同的连接方式：

你可以使用SSH或者HTTP协议来连接仓库，一般情况下默认的是HTTP，SSH协议连接仓库只能使用SSH Keys作为身份验证方法，而HTTP则只能使用PAT认证，我这里使用的是PAT，其实两种都挺好用的

创建PAT：

1.  打开GitHub官网，点你的头像，找到settings ![2024-01-13T14:24:53.png](https://zhoushicheng.cn/legacy_imgs/2989277170.png)
    
2.  然后找到左边最下面的Developer settings（这个在最下面），点开后是这样的 ![2024-01-13T14:26:05.png](https://zhoushicheng.cn/legacy_imgs/3869331474.png)
    
3.  直接点到Personal access tokens下面的Tokens(classic) ![2024-01-13T14:26:59.png](https://zhoushicheng.cn/legacy_imgs/4010743182.png)
    
4.  右上角的Generate new token，选择带classic的那个
    
5.  然后进入一个页面让你填写Note, Expiration, Select scopes啥的，Note就是给个备注，比如你是用在哪台电脑上的，什么软件里的啥的， Expiration是过期时间，然后select scopes就是这个token所拥有的权限，你可以指定，做好你的选项后直接点下面的Generate token就好了
    
6.  然后会出现一串字符串，网页提示让你保存，然后你真的一定要好好保存，因为这个token只会出现这一次，保存到安全的地方就好
    

这样你的PAT就申请好了

## 在本地初始化git仓库

先打开终端，cd到你的项目目录里，然后使用下面的这个命令将GitHub仓库作为远程仓库添加到本地仓库：

```
git remote add origin https://github.com/your-username/your-repository.git
```

然后这样就好了

## 推送本地代码到GitHub

用这个命令就行

```
git push -u origin main
```

这个main是你的默认分支名，GitHub现在默认都叫main了，如果是别的那就根据你GitHub上创建的仓库中的默认分支而变化

应该会问你要账号密码，用户名Username就是你的GitHub用户名，然后密码的话不要写你的登录密码，把刚刚的PAT的Token输进去就好了

## Enjoy

然后就可以使用vcs进行代码同步了，写完代码直接commit & push就行

* * *

git是真的好用，以后在实验室写完代码就可以直接push，回宿舍了直接用mac继续写，无缝衔接

* * *

# 其他

虽然我已经满足了我的需求，但是其实我还是有一些疑问，我也进行了了解

## 为什么在pycharm中提交代码的时候会出现两个分支，一个是main，一个是origin/main，但是实际上只有一个分支？

> 这是因为实际上我们在本地写代码的时候，是在main分支中进行操作的，而服务器上的代码实际上是叫做origin/main的，通过这样不同分支的操作来区别服务器上的代码和本地的代码

## git push, git merge, git pull, git fetch, git commit分别都是什么？

*   git commit是将我们当前的工作目录修改的文件保存为一个新的提交，这个提交包括了我们修改的文件，创建了一个提交对象。不过要注意，我们这一切操作都是在本地创建了新的提交对象，与远程服务器无关
*   git push就是将我们本地保存下来的这个更改对象上交到服务器，同步到远程仓库，只有执行了git commit和git push，我们才能在远程仓库中看到我们更改的代码
*   git fetch意思就是fetch，去获取最新的提交来保持同步。但是要注意的是，他仅仅是去获取最新的提交，但是不会与你的本地代码进行合并，所以哪怕是你执行了git fetch，也可能会看到本地代码文件和服务器上的代码不一致，这时候就需要git merge了
*   git merge就是用于合并代码，就是和刚刚git fetch中说的一样，把从远程服务器获取的最新提交与本地的代码合并，从而达到最新
*   git pull就是git fetch和git merge，将两个操作合并了起来，一步即可完成本地文件与远程服务器的同步。

## .gitignore

如果你使用多种不同的IDE进行代码编写，可能不同IDE会生成一些他们专属的项目配置文件，比如jetbrains的.idea文件夹，vscode的.vscode文件夹，他们在其他的IDE里看的很碍眼，你可以在项目根目录创建.gitignore文件，来关闭他们的上传，还你一个清静的项目目录

例如:

```
# .gitignore
.idea/
```

* * *