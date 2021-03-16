---
title: 如何让hexo个人博客被搜索引擎收录
date: 2021-03-10 21:05:55
tags: hexo
categories: Hexo博客系列
description: 如何让自己的hexo博客被搜索引擎收录，看看这篇文章
---
之前发过几篇博客，讲了如何使用hexo这个框架生成静态博客页面，在[hexo博客的定制化](https://blog.zenoxen.cn/2021/02/14/%E5%AF%B9%E5%8D%9A%E5%AE%A2%E5%B8%83%E5%B1%80%E8%BF%9B%E8%A1%8C%E4%B8%AA%E6%80%A7%E5%8C%96%E5%AE%9A%E5%88%B6/)这篇文章里也讲了我自己对Hexo博客是如何定制的，包括怎么启用评论、浏览量功能。
但如果大家都不知道你的博客，那评论也没啥意义了吧，这篇文章就讲一讲我自己是怎么让谷歌和百度收录我的博客的（百度最终还是没收录，不知道什么原因）。
# Github Pages的一个问题
如果你直接尝试让百度去收录你的github pages的话，会发现一个问题，百度抓取不到github pages的地址，由于历史原因，github把百度的爬虫给禁用了，所以虽然github pages是真的好用，但还是得忍痛割爱，换个路子。
# 使用Vercel网站托管服务
有很多网站提供静态网站托管服务，我试过Coding.net和vercel这两个，coding.net的托管服务是依托于腾讯云的，很早以前是免费的，但现在貌似有一定限制了，具体什么情况我有点记不清了；后来试了vercel，它也能提供免费的静态网站托管服务，而且操作起来比coding.net舒服，目前是免费的，这是它的官网[vercel](https://vercel.com/)。
## vercel如何与github联动
进入vercel，可以使用github账号登录。
![1](1.png)
coding.net与vercel都可以从github上导入repository，但vercel比coding.net好的一点在于第一次与github关联并授权以后，你以后往github上push的任何更改，都会同步到vercel上，而coding.net并不能这样（虽然可以通过hexo的deploy配置往coding.net自动部署）。
在vercel上选择New Project，然后如果你是用Github账号登录的话，这边会显示你的所有Github仓库，选择github pages的仓库即可。
![2](2.png)
中间导入的几个步骤就不截图了，按默认设置一直点下一步即可。最后成功导入后应该可以看到congratulations，点击那个visit就可以查看托管在vercel上的网站了。
![3](3.png)
这里有一点要注意的是，虽然内容与github pages上部署的那个网页一模一样，但vercel是一个独立的副本，只不过每次github pages那个仓库更新的时候，会同步更新到vercel这边而已。

## 使用自己的域名
在你的vercel项目这边找到settings项。
![4](4.png)
点击Domains项，可以看到里面有一条默认的域名项，这是vercel提供给你免费使用的，如果你的项目名没改的话，一般就是github里那个仓库名为前缀，看起来怪怪的，你可以自己点击edit来修改域名记录，注意一下，vercel.app是固定的域名，不要改掉，你可以改的是这个vercel.app前面那一堆。
![5](5.png)

到这里为止，实际上你已经拥有了一个与Github Pages不同的独立域名，即vercel.app，但如果你既想通过Vercel来部署博客站点，又想脱离vercel.app这个域名的话，唯一的方法就是买一个自己的域名了。我自己是通过阿里云买的域名，一个cn域名，第一年优惠很大，买下域名10块钱不到，所以比较建议买一个，过期了以后续不续倒是无所谓，早买早享受。

假设你已经买了一个自己的域名，那么仍然点击vercel这边的Domains项，输入你自己的域名，记住要带上主机记录，比如我的域名是zenoxen.cn，那么你在输入自己域名的时候，最好加一个主机记录，比如www.zenoxen.cn或者blog.zenoxen.cn，主机记录具体是什么倒无所谓。

![6](6.png)

这里假设我加的记录是www.zenoxen.cn，结果发现了一个Invalid Configuration的错误。

![7](7.png)

这实际上是因为没有将zenoxen.cn这个域名的www主机记录指向vercel服务器而造成的，换句话说，就是现在还每法通过www.zenoxen.cn来访问部署在vercel服务器的这个博客应用。一个比较简单的解决办法是给zenoxen.cn这个域名设置一个CNAME记录，将其指向vercel的服务器域名。

![8](8.png)

CNAME记录在你买域名的代理商那边就可以设置，比如我是在阿里云那边买的，那么我去阿里云那边找域名控制台。

![9](9.png)

添加vercel提供的那条CNAME记录，指向www.zenoxen.cn。

![10](10.png)

如此一来，那边自动就会检测到www.zenoxen.cn已经指向了vercel服务器，Invalid Configuration的报错也会消失了。

# 搜索引擎收录

接下来的一步就是让搜索引擎收录你的博客，无论你是使用vercel的域名，还是使用自己的域名，理应都可以被搜索引擎收录了。

实际上大部分搜索引擎收录流程都是类似的，我自己提交过Google和百度，方式都是大同小异的。

主流的搜索引擎都有几种收录方式：API推送、提交Sitemap、手动提交，这里简单介绍一下。

API推送是在特定页面上调用API



# 参考文章
[hexo设置主动推送](https://blog.csdn.net/liutao43/article/details/106324954/)
[如何让百度收录 GitHub Pages 个人博客](https://zhuanlan.zhihu.com/p/111773896)
