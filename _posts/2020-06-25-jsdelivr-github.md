---
layout: post
title: "使用 jsDelivr CDN 加速 Github 仓库的图片"
date: 2020-06-25 00:00:00
---

## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

![](/images/2020/06/jsdelivr/Github+jsDelivr.png#alt=Github%2BjsDelivr)

jsDelivr 是国外的一家优秀的公共 CDN 服务提供商，也是首个「打通中国大陆（网宿公司运营）与海外的免费 CDN 服务」。
jsDelivr 有一个十分好用的功能——**它可以加速 Github 仓库的文件**。我们可以借此搭建一个免费、全球访问速度超快的图床。

**声明：静态文件主要是缓存在 jsDelivr 的 CDN 节点上，确保 GitHub 承受最小的负载，并且你还可以从 GitHub 仓库获得快速简便的静态文件托管。**

> jsDelivr is a public, open-source CDN (Content Delivery Network) developed by ProspectOne, focused on performance, reliability, and security. It is free to use for everyone, with no bandwidth limits.

jsDelivr is the only public CDN with a valid ICP license issued by the Chinese government, and hundreds of locations directly in Mainland China.


## 主要思路

将图片上传到指定 Github 仓库位置，再利用 jsDelivr 获得图片加速后的 url。

使用效果：[点击访问测试图片](https://cdn.jsdelivr.net/gh/ailsio/my-site@main/static/CY/CY.jpg)

## jsDelivr 配置

其实就是设定自定义域名。我的设定是：

创建一个 GitHub 仓库作为图床仓库，上传提交图片到仓库中

```
https://cdn.jsdelivr.net/gh/ailsio/hello-blog@main
```

其中：

- **gh** 表示来自 Github 的仓库
- **ailsio/hello-blog** 仓库的具体位置
- **main** 仓库的分支

好的，在此就配置完成。图片链接形式如下：

```
https://cdn.jsdelivr.net/gh/ailsio/my-site@main/static/CY/CY.jpg
```

## 结语

- 使用 jsDelivr 加速静态文件访问，能够优化博客体验。
- 在 Github 存储图片，利于博主对于图片的掌控。

Github 仓库的容量有 1G 的上限，对个人博客来说绰绰有余。

Enjoy
