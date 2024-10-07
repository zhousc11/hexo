---
title: 宝塔面板提示SQL密码错误
tags:
  - 乱折腾
  - server
  - database
  - bt-panel
excerpt: >-
  在宝塔面板中新建网站的同时，勾选了新建MySQL数据库，但是在数据库列表中又看不到新建的数据库，点击“同步数据库”按钮却显示密码错误，无法操作数据库，猜测可能是一些原因导致数据库密码被改了，但是宝塔面板内没有更新导致的。  

  好吧，有问题就去解决它吧  

  ...
date: 2023-03-09 10:35:00
categories:
---

在宝塔面板中新建网站的同时，勾选了新建MySQL数据库，但是在数据库列表中又看不到新建的数据库，点击“同步数据库”按钮却显示密码错误，无法操作数据库，猜测可能是一些原因导致数据库密码被改了，但是宝塔面板内没有更新导致的。 好吧，有问题就去解决它吧 ...
<!-- more -->
在宝塔面板中新建网站的同时，勾选了新建MySQL数据库，但是在数据库列表中又看不到新建的数据库，点击“同步数据库”按钮却显示密码错误，无法操作数据库，猜测可能是一些原因导致数据库密码被改了，但是宝塔面板内没有更新导致的。

好吧，有问题就去解决它吧

# 环境

*   CentOS 7.9 2009
*   宝塔面板7.9.8
*   MySQL 5.6.50
*   phpMyAdmin 5.2

# 强制修改SQL密码

这肯定是MySQL的密码改了，但是宝塔没有更新密码，从网上找了几种方法，最后尝试成功了以下这一种方法：

## 修改MySQL配置文件

位置在`/etc/my.cnf`

```bash
vi /etc/my.cnf
```

在`[mysqld]`后加入一行

```mysql
skip-grant-table
```

![跳过权限检查](https://www.zhoushicheng.cn/legacy_imgs/skip-grant.png) 意思是跳过权限验证，这样就可以不用数密码登陆进MySQL了

## 使用ssh登录到服务器，重启MySQL服务

```bash
sudo systemctl restart mysqld
```

## 登录进MySQL管理界面

在命令行输入

```bash
mysql
```

这样就可以直接进入mysql了，不需要密码了，这就是`skip-grant-table`的用处了

## 在命令行输入

```mysql
flush privileges;
```

> 不要忘记输入最后的分号，不然会报错
> 
> ## 选择权限管理的数据库并修改密码
> 
> ```mysql
> use mysql;
> update user set password=password(“admin”) where user=“root”;
> ```
> 
> 不能忘记了后面的分号，双引号中间的admin就是密码
> 
> ## 更新权限并重启服务器就好了
> 
> 再运行一遍
> 
> ```mysql
> flush privileges;
> quit
> ```
> 
> 重启MySQL
> 
> ```bash
> systemctl restart mysqld
> ```
> 
> ## 最后在宝塔面板里更新密码即可
> 
> ![修改密码](https://www.zhoushicheng.cn/legacy_imgs/updatepasswd.png)