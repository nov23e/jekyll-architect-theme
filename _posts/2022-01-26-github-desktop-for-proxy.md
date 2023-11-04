---
layout: post
title: "如何给Github Desktop设置代理"
date: 2022-01-26 00:00:00
---

## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

使用Github Desktop克隆项目时只有十几、几十k的速度，一个30MB的项目要等半天。

于是寻找解决办法，本来以为是很简单的事情，却花费了超出预期的时间。
故记录下来

- 
先在Github Desktop设置里找，没有找到

- 
在网上搜，先搜索的英文，看了几个方法，但都是设置http/https代理的：[How Can I Configure GitHub Desktop/Windows to Work with a Proxy?](https://stackoverflow.com/questions/38615168/how-can-i-configure-github-desktop-windows-to-work-with-a-proxy?rq=1)

我需要的是设置socks5代理，虽然不太一样，但我还是瞎设置了一下，没有成功

- 
瞎设置，把`http`换成`socks`,输入


> git config –global socks.proxy socks://127.0.0.1:1080

git config –global socks.proxy socks5://127.0.0.1:1080

git config –global socks5.proxy socks://127.0.0.1:1080

git config –global socks5.proxy socks5://127.0.0.1:1080


- 
都不行

- 
**其实很简单，但是我太菜，不会…**

- 
后来转念一想，外国人只是在公司内网需要代理，和我们不一样，搜中文的教程才对

- 
果然，中文教程一大把

- 
[看到一篇X先生说：通过 SS 代理加快 GitHub Clone 速度](https://www.jianshu.com/p/8e9af3a1b231)


![](/images/2021/05/adguard/adguard-2.png#alt=adguard)

- 

   - 里面第一个黑框，`http://127.0.0.1:1080`
- 

   - **原来只需要这样就可以，留下了没技术的泪水**

## 我的解决方案

打开`C:\users\username\.gitconfig`文件，最后加上

```
[http]
    proxy = http://127.0.0.1:1080
```

就可以让Github Desktop走本地代理

08/18更新

前两天又遇到问题，clone不下来`git：//`，经过半天摸索，现在文件内容是这样的：

```
[http]
    proxy = socks5://127.0.0.1:1080
[https]
    proxy = socks5://127.0.0.1:1080
[git]
    proxy = socks5://127.0.0.1:1080
```

（再次留下了没技术的泪水

## 参考资料

- [How Can I Configure GitHub Desktop/Windows to Work with a Proxy?](https://stackoverflow.com/questions/38615168/how-can-i-configure-github-desktop-windows-to-work-with-a-proxy?rq=1)
- [X先生说：通过 SS 代理加快 GitHub Clone 速度](https://www.jianshu.com/p/8e9af3a1b231)
- [如何给Github Desktop设置代理](https://kassadin.moe/2019/07/17/003-how-to-set-proxy-for-Github-desktop/)
