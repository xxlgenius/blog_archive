---
title: 搭建Hexo+Github Page的个人博客
date: 2023-05-21 
updated: 2023-06-17
top_img: https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/05/22/9952866c14e98311e84d35e1d138440a.png
cover: https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/05/22/9952866c14e98311e84d35e1d138440a.png
description: 记录博客的配置过程
categories: 博客
tags:
  - Github
  - Hexo
---

# 前言

这是本博客的第一篇文章！特此把本博客的配置过程记录下来作为纪念也顺便测试Hexo的相关功能。

本博客由Github Page+Hexo搭建而成。优点为不用担心被ddos，可以借由git的版本管理管理文件更安全。缺点也是有的，静态页面更新极为痛苦以及性能不高，不过免费大过天，还要啥自行车。

# 准备工作

1. 首先登录[Github](https://github.com)账号，新建一个储存库，名称为`用户名.github.io`
2. 安装[Git](https://git-scm.com/downloads)，或者安装[GitHub Desktop](https://desktop.github.com/)用来同步储存库
3. 安装[Node.Js](https://desktop.github.com/)（推荐安装最新版本），因为Hexo是基于Node.Js的，而且之后的所有包都是通过npm安装。
4. 拉取存储库

在windows资源管理器内右键可以打开`git-bash`命令行工具，输入

```bash
git clone https://github.com/用户名/用户名.github.io.git
```

或者在`Code`中点击`Open with GitHub Desktop`打开GUI工具克隆。

![Github Desktop](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/05/21/c9842726f7224034defa97b911b33152.png)

# 安装Hexo

进入储存库路径，右键打开git-bash **新建一个分支**并切换到新的分支。如果你还不会Git请参考：[廖雪峰的Git教程——创建与合并分支](https://www.liaoxuefeng.com/wiki/896043488029600/900003767775424)

然后输入如下代码

```bash
npm install -g hexo-cli
hexo init hexo-blog
cd hexo-blog
npm install
hexo g
hexo server
```

此时Hexo就会在Http://localhost:4000运行

# 安装主题

相信90%的人用Hexo都是贪图它的主题吧（我也是）。扫了一圈下来发现[matery](https://github.com/blinkfox/hexo-theme-matery)非常好看，而且适配性很强在桌面端、平板、手机等设备上均能很好的展现。功能也很强大集成了很多功能（我可以不用，但是必须要有），而且作者也很活跃。所以就是它了。

安装过程其实作者的[README](https://github.com/blinkfox/hexo-theme-matery/blob/develop/README_CN.md)已经说的十分详细了，就不再赘述了。

> 如果命令行没有使用科学上网配置，请套用CDN：[jsdelivr](https://www.jsdelivr.com/) 或者 使用代理克隆仓库。否则可能速度很慢甚至被404。

## Git设置（可选）

虽然你可以直接下载zip包或者clone项目到本地，但我认为最好的方法是fork版本库，然后在项目添加子模块。这样你不仅可以通过Git管理你的主题配置，最重要的是以后切换主题也十分方便，你的hexo项目仓库不会太臃肿。

![fork](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/08/c67d61d6cce81c93d21a43faf1f9b1eb.png)

添加子模块Git指令：

```bash
git.exe submodule add   -- "https://github.com/你的用户名/hexo-theme-matery.git" "hexo-blog/themes/hexo-theme-matery"
```



# 配置utteranc

matery作者虽然已经配置了很多评论项目，但是有的被墙了，有的停止更新了，有的有安全风险。作者虽然推荐utteranc但是却没有配置，需要手动配置。

## 安装utteranc

1. 创建一个公开仓库

![创建仓库](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/05/21/e9797487107f16678c7384a50c9f1d34.png)

2. 安装[utteranc](https://github.com/apps/utterances)，直接点击`install`即可
3. 选择刚才创建的储存库（不建议选择所有仓库，一旦token泄露，则别人可以操控你的所有公有库）然后点击`install`。

![选择储存库](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/05/21/ff8132a71debaeffe546be63b8961387.png)

4. 配置建议默认，主题可以按需求修改，之后可以得到一个配置代码。保存一下，之后会用到

   ![配置文件](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/05/21/e38528e6c9ab9eff7fa25524dbbc5aed.png)

## 修改matery的文件

1. 修改**themes/hexo-theme-matery/_config.yml**文件，加入如下代码

```yml
utteranc:
  enable: true
```

2. 修改**themes/hexo-theme-matery/layout/_partial/post-detail.ejs **加入如下代码

```javascript
<% if (theme.utteranc && theme.utteranc.enable) { %>
<%- partial('_partial/utteranc') %>
<% } %>
```

3. 新建**themes/hexo-theme-matery/layout/_partial/utteranc.ejs**并写入

```javascript
<div class="card" data-aos="fade-up">
    <div id="utteranc-container" class="card-content">
		刚才utteranc生成的配置代码
        </script>
    </div>
</div>
```

4. 修改**themes/hexo-theme-matery/layout/contact.ejs**，在137行左右加入如下代码

```javascript
<% if (theme.utteranc && theme.utteranc.enable) { %>
    <%- partial('_partial/utteranc') %>
<% } %>
```

此时使用代码`hexo server`开启服务即可看到效果。



# 发布到GitHub

1. 安装hexo-deployer-git

```bash
npm install hexo-deployer-git --save
```

2. 修改根目录的`_config.yml`文件

```
deploy:
  type: git
  repo: https://github.com/你的用户名/你的用户名.github.io.git
  branch: main
  token: 你的token
```

[点此处](https://github.com/settings/tokens)来新建一个token，选择经典Token，然后勾选repo，然后点击生成即可看到token（一定要及时保存，因为token只会显示一次，若没有保存只能重新申请）

![image-20230521214205257](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/06/17/1582ac61cf6bf2dfeabebf582c5c014d.png)

![创建token](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/05/21/af29264919e17f132a99776bbebee047.png)

3. 部署到GitHub
安装[hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)`npm install hexo-deployer-git --save`
修改`_config.yaml`文件中`deploy`的配置:
```
deploy:
  type: git
  repo: https://github.com/用户名/用户名.github.io.git
```
```bash
hexo clean #清理文件
hexo g     #生成文件
hexo d     #上传文件
```


此时在浏览器输入http://你的用户名.github.io/就可以看到配置好的博客了，如果没有则等几分钟即可。

# 修改域名与开启HTTPS

虽然现在已经可以访问网站但是域名却是Github的，对于我自己有域名的还是想通过自己的域名访问，这样及时以后迁移网站也方便很多。

1. 打开你的DNS管理页面，在域名创建A记录到如下几个IP

```ipv4
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

​	若想开启ipv6访问也可以创建AAAA记录，官方文档建议至少创建一个A记录，因为 IPv6 的全局采用速度缓慢。

```ipv6
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
```

> IP来源于GitHub[官方文档](https://docs.github.com/zh/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)

​	然后在www子域创建CNAME规则指向`你的用户名.github.io`

> 如果配置顶点域名（如example.com），则一定要配置www子域。

2. 打开配置Hexo的储存库，点击`setting`然后选择`Pages`。然后在`Custom domain`中填入你的域名，等待DNS验证通过后就可以了。等待几分钟后就可以开启强制HTTPS了。

![配置域名](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/05/21/ea55d3a98d54fbccf2e65aa680a45cda.png)

3. 在/source目录下添加文件命令为`CNAME`,在里面添加你绑定的域名，否则，使用`hexo d` 上传的时候会删除github的cname设置

---
**2023-06-17更新**

# 在线编写&发布文章
可以参考我的另一篇文章：[hexo在线可视化编辑写作](http://blog.xxlgenius.com/2023/06/17/hexo%E5%9C%A8%E7%BA%BF%E5%8F%AF%E8%A7%86%E5%8C%96%E5%86%99%E4%BD%9C/)

# 参考文献

> [GoodLuck@hexo在matery主题下集成utteranc评论插件](https://liukgg.github.io/2021/08/15/hexo/hexo-zai-matery-zhu-ti-xia-ji-cheng-utteranc-ping-lun-cha-jian/#toc-heading-3)