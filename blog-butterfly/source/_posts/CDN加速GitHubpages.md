---
title: 又拍云CDN加速访问Github Page
date: 2023-07-2 
updated: 2023-07-2 
top_img: https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/07/04/dcd5b6846059a0f0d74f1d730675f9c2.png
cover: https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/07/04/dcd5b6846059a0f0d74f1d730675f9c2.png
description: 使用又拍云CDN优化国内访问速度
categories: 博客
tags:
  - Github
  - Hexo
  - CDN
---

# 前言

之前一直挂科学上网访问GitHub，所以对于GitHub的访问速度没有什么感觉。直到前段时间直连访问了一次博客，只能说和被墙也没什么两样。所以CDN对于Github Page来说就是必需品了。

# 为什么是[又拍云](https://www.upyun.com/?utm_source=lianmeng&utm_medium=referral)

又拍云的代金券并不是按月发放，而且按年。即这个月没用完的额度依然可以在今年内使用，这样的话即使有个别几个月超出免费额度也不会有问题。所以又拍云的免费额度反而是最实在的。

当你的网站人开始多起来以后，免费的CDN流量不够的时候。可以申请`推广大使`，每年有1000元的抵用券。非常适合中小型网站。

而且又拍云也支持对象存储，可以搭建图床

缺点：需要在加速页面的页脚添加推广链接。

# 搭建过程

## 必要条件

- 域名已备案
- 一个支持线路解析的DNS（推荐华为云，无需实名制，免费版也可以设置1秒TTL）
- 一个已经实名认证的又拍云账号

## 申请又拍云联盟

申请免费额度需要在页面添加推广链接

### 在matery主题上添加又拍云链接

1. 将又拍云的logo上传到你自己的图床上，[下载链接](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/07/04/8fa59409ece2a4491180ed1dad9f459b.png)

2. 在`_config.yml`文件中添加如下配置

```yml
upyun:
  enable: true
  logourl: #又拍云logo链接
  url: https://www.upyun.com/?utm_source=lianmeng&utm_medium=referral #推广链接
```

3. 在`/layout/_partial/footer.ejs`文件的大约第107行添加如下代码

```javascript
<div>
<% if (theme.upyun.enable) { %>
    <span id="upyun">
    <a href="<%- url_for(theme.upyun.url) %>" target="_blank">
        由
        <img src="<%- theme.jsDelivr.url %><%- url_for(theme.upyun.logourl) %>"    
        style="height: 50px; vertical-align:middle"/>
        提供CDN支持 
    </a>
</span>
<% } %>
</div>
```

添加后如图
![](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/07/04/561f032759a26b6335221ff5a59c17ed.png)

### 申请抵用券

打开[又拍云联盟](https://www.upyun.com/league)并登录

然后点击**立即申请**并填入相关信息，如图

![](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/07/04/21af6596c2a4be32a98d675ffcfa835e.png)

之后需要审核1-2天，耐心等待即可。



### 添加SSL证书

在等待审核的时间里，可以申请一下SSL证书

打开[又拍云SSL证书](https://console.upyun.com/toolbox/ssl/)

点击**申购证书**就可以申请免费的单域名证书，**证书管理**中可以上传自有证书

又拍云支持自动续签 Let's Encrypt 证书还是挺方便的



## CDN设置

1. 登录又拍云，进入[控制台](https://console.upyun.com/dashboard/)，创建CDN服务

​		配置如图所示

![](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/07/04/03de957788d21eeded449642b3ee6f2d.png)

​		点击创建后会生成专属CNAME



2. 打开DNS管理界面将域名的国内线路解析设置为CNAME

​		国际线路解析设置参考我的文章：[搭建Hexo和GithubPage的个人博客](https://xxlgenius.com/2023/05/21/%E6%90%AD%E5%BB%BAHexo%E5%92%8CGithubPage%E7%9A%84%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/)

3. 打开强制HTTPS，如图

![](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/07/04/bdf6423eb39d2d063dd3515a1222b3bb.png)

5. 剩余的配置可以参考介绍，视情况开启，一般都是用来优化性能和流量的，不开也不影响使用。

6. 设置好以后需要等一会访问，等CDN缓存完资源。也可以在控制台手动强制刷新，如图

![](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/07/09/413226d3077620e7329232b7a8bd12f2.png)



# 其他免费CDN的常见服务商

## 比较推荐

- [FreeCDN](https://www.freecdn.pw/)
  免费套餐只能绑定一个域名，但是有20G的ddos防御
- [多吉云](https://www.dogecloud.com/price?product=cdn)
  每月20G的免费流量，可以尝试
- [上海云盾](https://www.yundun.com)
  不限流量，但是带宽限制5M
  可以设置主站和备用站，主站离线可以自动切换到备用站

## 不推荐

[Cloudflare](https://www.cloudflare.com/)
没有国内的节点，在一些地区甚至是减速器

- [七牛云](https://www.qiniu.com/)
  只有HTTP流量是免费的
- [加速乐](https://defense.yunaq.com/jsl/)
  HTTPS不免费
- ~~百度云加速~~
  2023年5月5日起百度云加速不支持创建免费域名。地主家也没余粮了.jpg



# 总结

虽然介绍又拍云的时候写的像软文，但是确实很符合我的需求。大家可以根据自己的需求来选择其他家的CDN服务。适合自己的才是最好的。