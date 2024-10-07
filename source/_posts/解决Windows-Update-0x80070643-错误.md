---
title: 解决Windows Update 0x80070643 错误
tags:
  - 小技巧
  - Windows
  - windows update
  - issue
excerpt: |-
  在学校的电脑本来正常开启Windows Update，接受更新，但是不知道为什么突然报错了，检查更新的时候，显示`0x80070643`错误，类似这样：

  更新都不能被正常安装，看起来很烦人，解决了

  常规做法就是去搜索了下网上的社区，看看有没有什么解决方...
date: 2024-01-31 12:58:00
categories:
---

在学校的电脑本来正常开启Windows Update，接受更新，但是不知道为什么突然报错了，检查更新的时候，显示\`0x80070643\`错误，类似这样： 更新都不能被正常安装，看起来很烦人，解决了 常规做法就是去搜索了下网上的社区，看看有没有什么解决方...
<!-- more -->
在学校的电脑本来正常开启Windows Update，接受更新，但是不知道为什么突然报错了，检查更新的时候，显示\`0x80070643\`错误，类似这样：

![2024-01-31T04:27:55.png](https://zhoushicheng.cn/legacy_imgs/3869358462.png)

更新都不能被正常安装，看起来很烦人，解决了

* * *

常规做法就是去搜索了下网上的社区，看看有没有什么解决方法，找到了一篇Microsoft Answers的文档：

> 错误代码0x80070643，请问您系统是否右安装三方安全和优化类的软件，若是有建议您卸载，然后尝试以下操作：

> Cortana中输入“Windows defender”、回车后点击Windows defender安全中心，左侧启用Windows Defender防火墙。

> 然后以下操作：

> 1.  windows+R后，输入services.msc，回车

> 2.  找到Windows Update，手动停掉

> 3.  定位到C:\\Windows\\Software Distribution，清掉datastore里的内容

> 4.  重启Windows Update，重新检查更新。

实际上我按照这个走了一遍，根本没用，还是依然报错。下面还有第二个解决方案：

> Windows+S后输入CMD，右键搜索结果列表中的命令提示符，右键以管理员身份运行下面的命令：

> net stop wuauserv

> net stop cryptSvc

> net stop bits

> net stop msiserver

> Ren C:\\Windows\\SoftwareDistribution SoftwareDistribution.old

> Ren C:\\Windows\\System32\\catroot2 Catroot2.old

> net start wuauserv

> net start cryptSvc

> net start bits

> net start msiserver

然而还是没用，错误依然存在，就没办法。

对于社区里的这个提问题的人有帮助，但是对我还是没用，于是我只能去再搜索一下。

我又去检查了一下服务，在`service.msc`内，发现很多Windows Update相关的服务都没有打开，于是我就打开了那些相关的我认为有用的服务，然后重新检查安装更新，结果还是不行

# 我的解决方案

查阅了一下英文社区，发现了另一个可能导致错误的原因：`WinRE恢复分区空间不足`

应该是Windows Update在更新的时候，要借助一个Win RE分区，备份原有影响到的文件，帮助进行更新。不过可能需要安装的这个更新比较大，Win RE分区不够用，所以导致更新总是失败吧。所以我们就需要来调整这个分区的大小，让他能够正常更新。

> Microsoft has changed how it updates PCs that run the Windows Recovery Environment (WinRE). WinRE will be updated using the monthly cumulative update. This change only applies to PCs that get updates from Windows Update (WU) and Windows Server Update Services (WSUS). This change starts on June 27, 2023, for the Windows 11, version 22H2 cumulative update.

> Some PCs might not have a recovery partition that is large enough to complete this update. Because of this, the update for WinRE might fail. You will receive the error message, "Windows Recovery Environment servicing failed.” To help you recover from this failure, this article provides instructions to manually resize your recovery partition if you get a system ErrorPhase of 2. This requires your device to have the recovery partition after the OS partition. Use the steps below to verify this.

这是ms kb原文，更新确实是需要Win RE分区原文，某些PC确实可能没有足够的空间来完成更新，所以可以调整这个分区的大小帮助我们完成更新。

看了下磁盘管理，需要调整的应该就是系统盘后面那个分区了，所以我们采用缩减系统盘大小，然后删除原有分区，再把闲置区域分配给他。

![2024-01-31T04:47:59.png](https://zhoushicheng.cn/legacy_imgs/1811401977.png)

* * *

1.  使用管理员权限打开CMD
    
2.  检查下WinRE的状态`reagentc /info`
    

*   如果已经启用了WinRE，在`Windows RE location`就会有一个路径，例如`Windows RE location: [file://%3f/GLOBALROOT/device/harddisk0/partition4/Recovery/WindowsRE]\\?\GLOBALROOT\device\harddisk0\partition4\Recovery\WindowsRE.`
*   注意`harddisk0`和`partition4`这两个索引序号

3.  禁用WinRE`reagentc /disable`
    
4.  调整分区大小 依次执行下面的指令
    

```cmd
diskpart

list disk

sel disk<系统硬盘索引>

list part

sel part<系统分区索引>

shrink desired=250 minimum=250

sel part<WinRE分区索引>

delete partition override
```

5.  创建分区 依次执行

```cmd
# 检查是GPT还是MBR
list disk  # 是GPT右边会有*，否则就是MBR

# GPT执行
create partition primary id=de94bba4-06d1-4d40-a16a-bfd50179d6ac
gpt attributes =0x8000000000000001

# MBR执行
create partition primary id=27

# 格式化
format quick fs=ntfs label=”Windows RE tools”
```

6.  检查、退出
    
    ```cmd
    list vol
    exit
    ```
    
7.  重新启用WinRE分区 `reagentc /enable`
    

`reagentc /info`

这样就解决好了，我的PC可以正常检查安装更新了。

![2024-01-31T04:58:08.png](https://zhoushicheng.cn/legacy_imgs/3481226480.png)

# 总结

今天我分享的是解决Windows Update错误0x80070643的经历。初始尝试标准修复步骤无效后，我发现问题可能是由于Windows恢复环境（WinRE）分区空间不足。通过调整WinRE分区的大小，包括禁用WinRE、使用diskpart调整分区、创建新分区，并重新启用WinRE，我成功解决了更新问题。这次经历不仅增强了我的技术能力，也让我更加自信。希望我的分享对你们有所帮助！

* * *

我是六六，是个小白，啥也不会，但是希望也能做一个自己觉得厉害的人！

参考文献：

1.  [KB5028997: Instructions to manually resize your partition to install the WinRE update](https://support.microsoft.com/en-us/topic/kb5028997-instructions-to-manually-resize-your-partition-to-install-the-winre-update-400faa27-9343-461c-ada9-24c8229763bf)
    
2.  [错误 0x80070643](https://answers.microsoft.com/zh-hans/windows/forum/all/%E9%94%99%E8%AF%AF-0x80070643/8bb02fc4-8330-4b57-a32c-652d58db4b95)
    
3.  [Windows Update issue (failed to install) , 0x80070643](https://learn.microsoft.com/en-us/answers/questions/1495451/windows-update-issue-(failed-to-install)-0x8007064)