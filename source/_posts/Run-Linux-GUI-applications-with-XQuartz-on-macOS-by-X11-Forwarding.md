---
title: Run Linux GUI applications with XQuartz on macOS by X11 Forwarding
tags:
  - 乱折腾
  - 小技巧
  - Linux
  - macOS
  - ssh
  - x11
  - x
  - mac
excerpt: >-
  >
  [上篇文章](https://blog.csdn.net/u011074091/article/details/135940020)英文版，随便写写水一下，练习下英语

  Reason

  It's complicated to explain why I w...
date: 2024-01-30 21:18:13
categories:
---

\> \[上篇文章\](https://blog.csdn.net/u011074091/article/details/135940020)英文版，随便写写水一下，练习下英语 Reason It's complicated to explain why I w...
<!-- more -->
\> \[上篇文章\](https://blog.csdn.net/u011074091/article/details/135940020)英文版，随便写写水一下，练习下英语

# Reason

It's complicated to explain why I want to run a Linux GUI application on mac.

I own a windows PC with outstanding performance at school, which is on all day long, so I an able to offline download some movies, open a virtual machine to better make use of Linux and so on. Meanwhile, during my winter vacation, CNKI will be able to accessed from the computer by Microsoft Remote Desktop, which is convenient for me to browse papers for my 3chuang competition. I have been using it well since I came home on 15th, Jan.

But it's hard to say my PC worked well, the Internet service of my school requires authentications. I was kicked offline since 29th, Jan while I was online since 15th, Jan. So my PC was offline to the internet which results in my disability to control my computer remotely.

Students and teachers have been off for many days, thus nobody can access my PC in person. How to deal with this? I was so anxious.

* * *

Then soon I realized that, though I cant access my PC from the internet, there's GPU server at school which is on all year long, and the server and my PC is in the same subnet. Maybe I can connect to the server and then connect to my PC, then I will be able to operate my PC to authenticate?

> Though my PC is offline, the LAN IPv4 address doesn't alter, which means LAN resources can access my PC.

But there's still a big problem: the os of the server is linux, I cannot use the gui of linux fluently, and Sunloginclient on the server is command-line only version, I cannot get a remote gui connection to the server, How can I cope with it?

Then it suddenly occured me that when I was using Xshell to open a jupyter lab server, many dialogues appears on opening the server, asking me if I need to forward x11 requests to Xmanager.

> I was curious at that time: what is a x11 request? Then I get to know that the GUI on linux is implemented with `X Window Server`, so forwarding x11 requests means that gui can be forwarded to the SSH client, making it not only have the ssh cli, but graphic user interface.

Then a search was performed by me, I soon got to know that forwarding x11 requests is like Microsoft's RDP Remote App, just like the native applications on client, which has a good experience. (It also feels like the fusion mode of `Parallel Desktop`)

Then I realized that though Linux has no official RDP client support, there's some third-party RDP client, helping me to connect to my PC.

RDP clients available on Linux:

*   Remmina
*   Gnome-connections

I searched the internet and found these two clients performs best of all, especially `Remmina`, with good user interface and a wide-range of protocols supported.

\==Supporting Network-Level-Authentication is the most significal point==, as there's no possibility for me to turn this off on my PC in person.

Thus I need to using x11 forwarding on this linux server, which enables me to connect to my PC to authenticate the school's network.

# Hardware & software envs

## Client

*   MacBook Air(M1, 2020)
*   macOS Sonoma 14.3(23D56)
    
    ## Server
    
*   Dell PowerEdge C4140
*   Ubuntu 20.04 LTS with Gnome Desktop

> WARNING! Gnome desktop is correctly installed on the server and you may consider installing one for your server if you do NOT own a desktop environment. The compatibility of X server is better for gui. So you can barely make it work with a `Wayland` desktop environment.

# Running Linux GUI Application on macOS by x11 forwarding

## Installing x11 client

The built-in terminal of macOS does not support x11 requests, so we need to download a software called `XQuartz`, you can directly download from its official website.

[XQuartz](https://www.xquartz.org/)

The software works well on both Intel-based & Apple Silicon Macs.

Just click Next during your installation.

> After installation, remember to log out your current user, which means you need to save your files and close your applications.

## Configure XQuartz

After installation, it should be located in the `Other` folder in your Launchpad by default. If you don not see the software, please try to seek it out or use Spotlight search.

Then Just open it with one click.

![](https://img-blog.csdnimg.cn/img_convert/75cb588a9a6f1b618e1324b4ef82d473.png)

The main interface of this software is supposed to be a terminal. Next, use `Command + ,` to open settings, tick this `Allow connections from network clients` in 'Security' tab. I do not understand what it is, maybe this allows connections from the internet.

![](https://img-blog.csdnimg.cn/img_convert/8f42a3e21ddb207ed7d0579eca0d768e.png) Then you can connect your server with XQuartz via SSH.

## Connect to the server

Just like general SSH connection, now you can yse SSH to connect to your server. BTW DO NOT forget to add a parameter `-X` or `-Y`, which means allowing X11 forwarding.

> In common cases, `-X` parameter is enough, but in some advanced 3D Applications such as `Maya`, `Blender` , it may not work properly, so just use `-Y` to apply a looser security policy.

Your command may look like:

```bash
ssh -p <portNumber> -X username@serverip
```

Then just type in your password or authenticate with SSH keys.

![](https://img-blog.csdnimg.cn/img_convert/cda250b0f4a7147c93fffbac86513acf.png)

Then you are able to execute your gui apps.

> You can run some little software to see if the window displays. For example, `xeyes` & `xclock` is good for testing. ![](https://img-blog.csdnimg.cn/img_convert/1c2f0f541fa7ef1a7bea093d196769e0.png)

Then you can run your gui applications directly, for me, I type in `remmina` , then I can see the graphic user interface!!!

* * *

The experience is acceptable for me via port forwarding in WAN, the latency and fluency is good.

![](https://img-blog.csdnimg.cn/img_convert/eacca06e3158494050a5eba1cc6c4fe7.png)

# Problems may occur

This time is successful for me, but some situations do happen sometimes. It's recommended for you to pay more attentions to common faults, such as

## SSH configuration file

You may need to check the SSH cofiguration files on the server which is located in `~/.ssh/config` or `/etc/ssh/ssh_config` , to see if there's a line `Forward X11 yes` to allow x11 forwarding.

## Windows clients

I use my Mac more often at home, but it should be the same for windows to configure the x11 forwarding with a third-party software. As far as i know:

*   Xmanager(Paid)
*   Xming
*   VcXsrv Windows X Server

If you succeed, exchanges are always welcomed!!!

My homepage: [六六Blog](https://www.zhoushicheng.cn)