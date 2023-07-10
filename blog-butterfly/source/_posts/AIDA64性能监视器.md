---
title: AIDA64性能监视器
date: 2023-06-03
updated: 2023-06-03
top_img: https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/08/79161f8143ad4f5fb085c3a5ba473b5f.png
cover: https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/08/79161f8143ad4f5fb085c3a5ba473b5f.png
description: 利用安卓平板作为windows的性能监视器，显示帧率，硬件占用率等信息，并实现与Windows系统同步息屏
categories: windows配置
tags:
  - Aida64
  - windows
---
# 前言
每次在玩游戏的时候我都习惯没事看看帧率，有时候帧率不稳定还需要开一个任务管理器来看是cpu还是gpu占用过高。AIDA64自带的性能监视器可以完美解决这个问题，所以斥巨资（100块）买了一个小米平板1刷了林萧战的系统，拿来当作AIDA64的显示面板。一开始使用的安卓的脚本软件，定时ping主机来作为判断条件。虽然很糙，但是能用。最近微软更新了一波防火墙，默认把ICMP的入站规则给关了。（微软罪大恶极，浪费了我几个小时）。更新的方法是使用bat脚本然后用adb来控制安卓设备，开关机的时候自动启动。但是win11的任务计划中没有关机的触发器（我不理解），还好微软的兼容性~~（屎山代码）~~很好，在组策略中还是保存的开关机的触发器，完美。

# 搭建过程

打开AIDA64设置并开启LCD选项即可。开启帧率显示需要安装[RTSS](https://www.guru3d.com/files-details/rtss-rivatuner-statistics-server-download.html)

## 主题使用
参考文章：[AIDA64 Remotesensor 副屏监控主题使用说明](https://www.bilibili.com/read/cv16164486)

## 主题推荐

* [主播女孩重度依赖](https://www.bilibili.com/video/BV1P94y1o7yE)
* [赛博酒保行动](https://www.bilibili.com/video/BV15i4y1C7WL)
* [persona5](https://www.bilibili.com/read/cv22232557)

> p5的主题是我做的如果有什么问题和建议可以提交issue

## windows自动控制安卓开关屏幕

使用该方法，你需要下载adb并在开发者选项中打开手机的usb调试以及通过网络调试并把手机/平板WiFi改成静态ip或者在路由器设置手机/平板IP与mac绑定（以上操作每个品牌的设备都有些许不同，如果操作有困难请百度）。

完成以上操作后，在windows添加环境变量，将adb所在路径添加到path下。

打开cmd/powershell，输入adb回车，不报错即可。

有线连接：

插上线以后设备会询问你是否允许调试，勾选始终允许。然后在cmd/powershell中运行 adb devices命令可以看到设备，如果没有请使用adb connect连接。

无线连接：

在cmd/powershell中使用adb connect 设备ip连接，第一次会失败提示未授权。在设备中选择始终允许调试。

运行adb disconnect 设备ip断开连接，然后adb connect 设备ip重新连接。此时运行adb devices命令可以看到设备。

如果无线连接有问题，请检查设备监听端口是否为5555，Windows的adb调试端口是否被占用，具体操作请百度。

在C:\Windows\System32\GroupPolicy\Machine\Scripts\Startup路径下，新建文本文档。在文档中添加如下命令：

```bat
@echo off

adb connect 你的设备ip

adb shell input keyevent 224
```

然后保存并将文件后缀修改为bat

在C:\Windows\System32\GroupPolicy\Machine\Scripts\Shutdown路径下，新建文本文档。在文档中添加如下命令：

```bat
@echo off

adb connect 你的设备ip

adb shell input keyevent 26
```

然后保存并将文件后缀修改为bat

运行脚本测试功能。第二个脚本运行后会关闭设备屏幕，第一个运行后会打开。如果没有请删除脚本@echo off命令并在最后添加pause命令。重新运行后可以看到报错信息。

然后win+R输入gpedit.msc打开编辑组策略。参考下图添加启动和关机脚本。脚本默认异步执行，不会减慢开机速度。
![](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/11/98a609c32887c4e56ff29018c3d876e1.png)

