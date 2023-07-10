---
title: 优化Windows快捷键设置，定制自己的工作流
date: 2023-06-10
updated: 2023-06-10
top_img: https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/12/96c4ae655690d6f6607f545bdd16c051.png
cover: https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/12/96c4ae655690d6f6607f545bdd16c051.png
description: 合理配置Windows快捷键，摆脱对鼠标的依赖
categories: windows配置
tags:
  - Autohotkey
  - windows
---
# 前言
我眼馋Linux的开放环境很久了，可以随意更换windows manager和桌面环境，真正的定制化。但是迁移系统的成本实在是太高了，目前在用的插件很多都是windows独占，跨平台意味着要重新构建整个操作习惯。最后还是想办法再优化一下windows好了~~（又不是不能用）~~

# [PowerToy](https://github.com/microsoft/PowerToys)

我实在想不明白微软为什么不把powertoy的功能集成到windows系统里。里面的每一个功能都在确确实实的解决windows了使用痛点，包括*始终置顶*、*键盘重映射*、*快速预览文件*、*快速调整图片大小* 甚至还集成了*wox*

上述功能或多或少都可以有其他替代品，powertoy只是集成在了一起，优点只是免费而已

接下来才是关键功能

## FancyZones






# Autohotkey

安装[autohotkey](https://www.autohotkey.com/)

由于现在网上很多脚本都是v1的版本。如果有需要可以使用：[v1,v2转换脚本](https://github.com/mmikeww/AHK-v2-script-converter)

## 获取电源选项的GUID

打开`CMD`或者`powershell`输入`powercfg -list`，会显示当前所有的电源计划的名称和对应的GUID

![](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/12/31e950b0d35e9e48da8eacf6143c6700.png)

**在`控制面板`-`电源选项`-`编辑计划设置`-`更改高级电源设置`中可以更细致的设置电源策略**

![](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/12/f617d1eeec22e147757b31792bf82ab0.png)

## AHK代码
具体AHK的键位绑定，参考[官方文档](https://www.autohotkey.com/docs/v2/)

```
^+1::
{
	Run ("C:\Windows\System32\powercfg.exe setactive 6ed08f3e-52a1-4d96-9ae0-2f6619c8cdfd")
	TrayTip "切换至节电模式";Windows消息通知
	Sleep 3000
	HideTrayTip
}


HideTrayTip() {
    TrayTip  ; Attempt to hide it the normal way.
    if SubStr(A_OSVersion,1,3) = "10." {
        A_IconHidden := true
        Sleep 200  ; It may be necessary to adjust this sleep.
        A_IconHidden := false
    }
}
```
## 设置开机启动

设置一个快捷方式在`C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp`文件夹下即可