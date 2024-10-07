---
title: '[Fixed]Windows Update 0x80070643 error'
tags:
  - 小技巧
  - Windows
  - windows update
  - en
excerpt: >-
  My school PC originally receives Windows Update normally, but recently somehow
  it suddenly encounters an error. When checking fo...
date: 2024-02-01 15:52:21
categories:
---

My school PC originally receives Windows Update normally, but recently somehow it suddenly encounters an error. When checking fo...
<!-- more -->
My school PC originally receives Windows Update normally, but recently somehow it suddenly encounters an error. When checking for updates, it says \`0x80070643\` error. Just like this:

![2024-01-31T04:27:55.png](https://zhoushicheng.cn/legacy_imgs/3869358462.png)

Any updates cannot be applied, which is really disgusting.

* * *

The normal solving method is to search the community on the internet to see if there is any methods to fix it. So I turned to Microsoft Answers for help and found a document:

> \[Translated from Chinese, mainly\] The error code 0x80070643, may I know if there is any third-party security or optimization software, if it is, it is suggested to uninstall them and then try the following operations: Type "Windows defender" in Cortana, press enter and click "Windows Defender Security Center", please left button to enable Windows Defender Firewall. Then try the following operations:
> 
> 1.  Windows + R, type in `services.msc` then enter
> 2.  locate Windows Updare, disable it mannually
> 3.  Locate to `C:\Windows\Software Distribution`, delete all files in `datastore`
> 4.  Reopen Windows Update and try to check for updates again.

In fact I did so above and it does not worked. But there's another solving method.

> Windows+S, type in cmd, find command prompt on the right, right click run the following commands using administrator privilege: net stop wuauserv net stop cryptSvc net stop bits net stop msiserver Ren C:\\Windows\\SoftwareDistribution SoftwareDistribution.old Ren C:\\Windows\\System32\\catroot2 Catroot2.old net start wuauserv net start cryptSvc net start bits net start msiserver

But it still didn't work, the error still exists. I am out of options.

However, it worked for the person in the community, so I had to do another searches to solve my problem.

# My Final Solotion

I searched the English community and discovered another possible cause of the problem: `Insufficient space left on WinRE recovery partition`.

Maybe during Windows Updates, a WinRE partition is used to backup the files affected to help perform updates. But the January update is a little huge which caused the problem.

So we need to reallocate the partition size so that we can update normally.

> Microsoft has changed how it updates PCs that run the Windows Recovery Environment (WinRE). WinRE will be updated using the monthly cumulative update. This change only applies to PCs that get updates from Windows Update (WU) and Windows Server Update Services (WSUS). This change starts on June 27, 2023, for the Windows 11, version 22H2 cumulative update.

> Some PCs might not have a recovery partition that is large enough to complete this update. Because of this, the update for WinRE might fail. You will receive the error message, "Windows Recovery Environment servicing failed.” To help you recover from this failure, this article provides instructions to manually resize your recovery partition if you get a system ErrorPhase of 2. This requires your device to have the recovery partition after the OS partition. Use the steps below to verify this.

This is the original text in Microsoft KB, and some PCs do fail to apply the update patch, so adjusting the partition size should help to finish applying update.

After checking Disk Management, it seemed that the parition to adjust is the on right after the system drive, so we can just reduce the system drive and finally distribute the freeed-up space to it.

![2024-01-31T04:47:59.png](https://zhoushicheng.cn/legacy_imgs/1811401977.png)

* * *

1.  Open cmd with administrator privilege
2.  Check the status of WinRE `reagentc /info`

*   if WinRE is enabled, a path should disappear in `WinRE location`, like this:`Windows RE location: [file://%3f/GLOBALROOT/device/harddisk0/partition4/Recovery/WindowsRE]\\?\GLOBALROOT\device\harddisk0\partition4\Recovery\WindowsRE.`
*   Pay attention to the index number of `harddisk0` & `partition4`

3.  Disable WinRE `reagentc /disable`
4.  Adjust the partition size
    
    ```bash
    
    diskpart
    ```
    

list disk

sel disk

list part

sel part

shrink desired=250 minimum=250

sel part

delete partition override

````
6. create the new partition
```bash
# Check if your disk is MBR or GPT
list disk  # an '*' should disappear if GPT, else MBR

# if GPT, execute
create partition primary id=de94bba4-06d1-4d40-a16a-bfd50179d6ac
gpt attributes =0x8000000000000001

# if MBR,execute
create partition primary id=27

# format
format quick fs=ntfs label=”Windows RE tools”
````

6.  Check and exit
    
    ```bash
    list vol
    exit
    ```
    
7.  Enable WinRE partition `reagentc /enable` `reagentc /info`

And it works!!!

![2024-01-31T04:58:08.png](https://zhoushicheng.cn/legacy_imgs/3481226480.png)

# Summary

Today, I'm sharing my experience of resolving the Windows Update error 0x80070643. After initial attempts with standard troubleshooting steps proved ineffective, I discovered that the issue might be due to insufficient space in the Windows Recovery Environment (WinRE) partition. By adjusting the size of the WinRE partition, which included disabling WinRE, using diskpart to alter the partition, creating a new partition, and re-enabling WinRE, I successfully resolved the update issue. This experience not only enhanced my technical skills but also boosted my confidence. I hope my sharing is helpful to you!