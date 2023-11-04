---
layout: post
title: "在Chrome中设置Google搜索为NoCountryRedirect"
date: 2021-04-14 00:00:00
---

## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

添加代理来科学上网时，使用Google进行搜索，Chrome浏览器默认会重定向到代理国所在的Google域名。
比如`https://www.google.co.kr/`,`https://www.google.co.jp/`等, 已经在`https://www.google.com`登录的Google账号就还得重登, 极度烦躁。

下面是几个固定使用`https://www.google.com`来进行搜索的方法

## 实现方式

介绍三种方式来实现。

### 固定访问地址

使用`google.com/ncr`来访问Google，ncr是NoCountryRedirect的缩写，此时Google会重定向请求到主站`https://www.google.com/`，而不会定向到访问IP所在的Google域名了

### 修改Chrome设置

1. 地址栏输入`chrome://settings/`进入Chrome设置页面
2. 找到“搜索引擎”项，点击“管理搜索引擎”

![](/images/2021/04/nocountryredirect/ncr-1.png#alt=Nginx)

3. 在“管理搜索引擎”页面，点击的“添加”按钮。
4. 在“添加搜索引擎”页面中，搜索引擎和关键字一栏设定为自己能识别的名字，此处命名为“Google NCR”，在网址一栏中填入”https://www.google.com/search?q=%s”

![](/images/2021/04/nocountryredirect/ncr-2.png#alt=Nginx)

5. 再设置刚添加的“Google NCR”条目为默认搜索引擎

![](/images/2021/04/nocountryredirect/ncr-3.png#alt=Nginx)

6. 之后在地址栏直接输入要搜索的内容时候，就会使用Google主站`https://www.google.com`来进行搜索了。

### 使用Chrome扩展

有一个名为“NoCountryRedirect (NCR)”的Chrome extension是专门用来解决这个问题的。[插件传送门](https://chrome.google.com/webstore/detail/nocountryredirect-ncr/ciboebddidackjicoeoiigdnbmchkdll)

根据扩展的描述，其实就是在输入`www.google.com`时自动在URLS后面添加上`'/ncr'`来实现的。此插件只对google.com和blogspot.com有效。插件描述如下

> Makes sure you stay on google.com and blogspot.com by automatically adding ‘no country redirect’ (‘/ncr’) to those URLs

THIS EXTENSION ONLY WORKS FOR BLOGSPOT.COM AND GOOGLE.COM, NO OTHER DOMAINS!


## 总结

本着能不加扩展就不加扩展和最小操作的原则，最终选择了修改Chrome设置的方法。

## Reference

- [How do I change my default search country? Google thinks I am in a different country](https://productforums.google.com/forum/#!topic/chrome/zXRG7-AEgfw)
- [如何强制 chrome 地址栏使用 google.com 搜索？](https://www.v2ex.com/t/159920)
- [在Chrome中设置Google搜索为NoCountryRedirect](https://www.jibing57.com/2018/03/15/how-to-set-chrome-to-use-google-with-NoCountryRedirect/)

Enjoy
