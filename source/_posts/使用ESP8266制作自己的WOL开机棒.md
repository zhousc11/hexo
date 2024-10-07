---
title: 使用ESP8266制作自己的WOL开机棒
tags:
  - 乱折腾
  - esp8266
  - wol
  - C
  - 物联网
excerpt: >-
  # 使用ESP8266物联网开发板制作属于自己的WOL开机棒

  起因

  暑假在家的时候感觉很无聊，刚好高中的时候接触了一些关于物联网开发的内容，一直觉得很有意思，然后当时也心血来潮突然很好奇Apple的HomeKit生态如何，同时在上学的时候也需要经常使用家里...
date: 2023-03-31 11:22:00
categories:
---

\# 使用ESP8266物联网开发板制作属于自己的WOL开机棒 起因 暑假在家的时候感觉很无聊，刚好高中的时候接触了一些关于物联网开发的内容，一直觉得很有意思，然后当时也心血来潮突然很好奇Apple的HomeKit生态如何，同时在上学的时候也需要经常使用家里...
<!-- more -->
\# 使用ESP8266物联网开发板制作属于自己的WOL开机棒

## 起因

暑假在家的时候感觉很无聊，刚好高中的时候接触了一些关于物联网开发的内容，一直觉得很有意思，然后当时也心血来潮突然很好奇Apple的HomeKit生态如何，同时在上学的时候也需要经常使用家里的电脑，恰好家里还有一些开发板，于是参考网上的教程，自制了一个WOL开机棒。

## 需要准备的

*   ESP8266\*1
    *   配microUSB电源线、适配器
*   Blinker·电灯物联网账号\*1
*   支持WOL唤醒的PC\*1
    
    * * *
    
    ## 过程
    
    ### 获取API Key
    
    由于项目是部署在“点灯”物联网平台上，所以我们需要获取一个API Key，并把它记下来，直接在手机上下载App，打开后，点击右上角的加号，选择“点灯独立设备”，选择“网络接入”即可，我们就这样获取到了一串数字+字母组成的API Key，**请一定要记住它**。
    

### 新建一个按钮组件对象

我们的WOL实际上是请求一个局域网内的设备向整个网络发布一个幻数据包，并在整个网络内找到符合数据包的主机，然后需要被开机的设备收到指令后执行上电开机操作。

在点灯App中打开你刚刚创建的设备，此时应该是没有组件，空白的，然后请点击左上角的编辑按钮，把下面的按钮拖到上面的空白区域，再点击这个按钮，选择你喜欢的样式，**重要的是下面的这个组件键名，请一定要记住这个值，后续会用到**。

### 在PC上打开WOL功能

WOL，都是Wake On LAN的意思，顾名思义，肯定是需要一张支持WOL的网卡以及在同一个内网的。对于现在的台式PC来讲，一般只要不是太老（一般为2012年后）的板载或者PCIe网卡都支持WOL唤醒，只需要在BIOS设置中打开板载网卡唤醒或者PCIe设备唤醒，然后再控制面板中打开网络唤醒即可。

![在控制面板中打开WOL功能](https://zhoushicheng.cn/legacy_imgs/999870336.png)

### 装驱动

先去[下载](https://www.wch.cn/downloads/CH341SER_EXE.html)好ESP8266的串口驱动，直接Install安装即可，**_安装完了一定要记得重新拔插一下USB接口_**

### 配置开发环境

等一下就要往开发板里面烧录固件了，还是得配置一下开发环境的

1.  先去Microsoft Store[下载](https://www.microsoft.com/store/apps/9nblggh4rsd8?ocid=badge)Ardunio IDE（Windows 10+）或者去[官网下载](https://www.arduino.cc/en/software/)，然后安装即可。
2.  然后在文件-附加开发板管理地址中输入一下网址，确定
    
    ```
    https://raw.githubusercontent.com/SpacehuhnTech/arduino/main/package_spacehuhn_index.json,
    https://arduino.esp8266.com/stable/package_esp8266com_index.json
    ```
    
    然后在开发板-开发板管理器里搜索找到ESP8266这个SDK，作者是ESP8266Community，安装，版本似乎都可以（我用的是3.0.2），安装完成就可以关掉了。
    

> Tips:如果你的网络环境不是很好，下得很慢（需要连接GitHub），你可以参考这篇来自Ardunio的[教程](https://www.arduino.cn/thread-76029-1-1.html)

安装完就完事了

3.  安装点灯的库 先下载[这个](https://diandeng.tech/dev)，然后如下图添加库
    
4.  完成
    
    ### 烧录固件
    
5.  先去设备管理器看看开发板是哪一个串口，我这里没有插开发板，所以就是这样 ![设备管理器界面](https://zhoushicheng.cn/legacy_imgs/2328769795.png)
    
6.  将下面的代码输入Arduino的输入框中，然后根据注释修改自己的代码就好，然后直接在Ardunio中上传即可
    
    ```cpp
    
    #include <Pinger.h>
    #include <PingerResponse.h>
    ```
    

# define BLINKER\_WIFI

# include <Blinker.h>

# include <WiFiUDP.h>

char auth\[\] = "**\*\*\*\***";//app中获取到的Secret Key(密钥) char ssid\[\] = "MERCURY\_****";//填入wifi账号 char pswd\[\] = "\*\*\*\*****";//填入wifi密码 byte mac1\[\] = {0xAA, 0xAA, 0xAA, 0xAA, 0xAA, 0xAA};//唤醒目标电脑的mac为40 B8 76 A8 38 A6 ,替换为自己的电脑mac地址 byte preamble\[\] = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF}; IPAddress ip(192, 168, 0, 255); //幻数据包需要在局域网中广播发送 要控制的电脑的网段的255地址 //int pingResult; WiFiUDP UDP; //建立一个WiFiUDP对象 UDP

// 新建组件对象 BlinkerButton Button1("btn-28v");//注意：内容替换为app中添加按键的数据键名 int GPIO = 0; //定义继电器输入引脚为GPIO/0

// 按下BlinkerAPP按键即会执行该函数 // 按下按键即会执行该函数 void button1\_callback(const String & state) { UDP.beginPacket(ip, 7); //UDP发送到目标（IP，端口） UDP.write(preamble, sizeof preamble); //写入包头(FF,FF,FF,FF,FF,FF) for (byte i = 0; i < 16; i++) { UDP.write(mac1, sizeof mac1); } Serial.println("唤醒主机"); UDP.endPacket(); } void setup() { // 初始化串口，并开启调试信息，调试用可以删除 Serial.begin(115200); BLINKER\_DEBUG.stream(Serial); // 初始化IO pinMode(GPIO, OUTPUT); digitalWrite(GPIO, HIGH);

// 初始化blinker Blinker.begin(auth, ssid, pswd); Button1.attach(button1\_callback);

} void loop() { Blinker.run(); }