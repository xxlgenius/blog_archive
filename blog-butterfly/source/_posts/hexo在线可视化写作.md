---
title: hexo在线可视化编辑写作
date: 2023-06-17 
updated: 2023-06-17
top_img: https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/17/b9f5cbc9eefe927ed3b08cb517a97f39.png
cover: https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/17/b9f5cbc9eefe927ed3b08cb517a97f39.png
description: 利用Github Codespaces来编辑, 预览,管理 hexo博客
categories: 博客
tags:
  - Github
  - hexo
---

# 前言
之前写了如何利用Github Page来托管hexo博客。之前的几篇文章都是在一台电脑上用VScode来写，体验也是蛮好的。直到买了一个笔记本才体会到了hexo的痛点。无法多终端方便的同步文章和配置，始终需要通过git同步，然后使用`npm`安装一大堆的依赖。
使用~~微软~~Github的Codespaces可以完美解决所有问题。

# Codespaces

## 什么是Codespaces

GitHub Codespaces 是 GitHub 提供的一项云开发环境服务，它允许开发人员在云端的编程环境中编辑、构建和调试代码。通过 Codespaces，开发人员可以轻松地访问和管理项目代码，而无需在本地设置和配置开发环境。~~以上内容由ChatGPT3.5生成~~

## 使用Codespaces

入口在GitHub仓库的`<>Code`按钮里，如图所示

![Codespaces入口](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/17/8423b87bef549b67de2e2be24ff13206.png)

点击`Create codespaces on <branch>`就可以开启一个Linux虚拟机，并且配置好了[code-server](https://github.com/coder/code-server)并且可以选择在浏览器、VScode、 Jetbrains Gateway和JupyterLab中打开。

甚至你可以开启多个Codespaces，但是每个月有时间限制，免费帐户可以使用120h，pro账户180h

![](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/17/d73b5123232a93f4c2959f6c1352ea6c.png)

> 如果不够用的话可以买一个服务器自己搭建code-server 或者 在[zeabur](https://zeabur.com/zh-CN)白嫖

说白了，其实就是个云端的VScode编辑器，如图

![codespaces在浏览器使用界面](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/17/075a6f769694148fc0888185d807e020.png)

顺便一提，虚拟机的配置如图

![微软真有钱.jpg](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/17/7927118126f5e998df7bcac485537ac4.png)

Linux系统信息如图，约等于白嫖了一个服务器，而且各种常用环境已经配好了

![Linux系统信息](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/17/78d3c17f7777bc4c1791a95b0d1fbbe3.png)

## 预览hexo

在本地的时候每次发布前都会使用`hexo s`命令查看以下效果。

在Codespaces中会自动将端口转发出去，具体如图

![端口转发](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/17/49d50eacf6634d0eb376ec353e1aa782.png)

甚至给了域名和https，真的好贴心，开箱即用属于是

~~（甚至可以拿来不写代码直接白嫖服务器部署点项目什么的）~~

> Tips:如果你想将hexo的项目文件和public文件分开，部署的时候可能会无权限上传到仓库。可以生成ssh密钥并添加到账户中，然后将仓库地址更换成ssh连接的地址

# 总结

微软真有钱
