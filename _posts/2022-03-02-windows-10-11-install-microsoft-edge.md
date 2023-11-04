---
layout: post
title: "在 Windows 11/10 中卸载 Microsoft Edge 浏览器的简便方法"
date: 2022-03-02 00:00:00
---

## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

Microsoft Edge 预装为 Windows 11 和 Windows 10 中的默认 Web 浏览器。当您尝试使用控制面板或设置应用程序卸载 Microsoft Edge 时，您会发现卸载选项丢失或显示为灰色。 在本教程中，Secy将向您展示一种在 Windows 11 / 10 中卸载 Microsoft Edge 的简单方法。
![](/images/2022/03/edge/edge-1.png#alt=%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%8D%B8%E8%BD%BD%20Microsoft%20Edge%20%E6%B5%8F%E8%A7%88%E5%99%A8)

## 如何在 Windows 11 / 10 中卸载 Microsoft Edge

1. 
右键单击桌面上的 Microsoft Edge 快捷方式，然后从上下文菜单中选择“打开文件位置”。

2. 
文件资源管理器将打开文件夹：C:\Program Files (x86)\Microsoft\Edge\Application。 只需双击以 Microsoft Edge 版本号命名的文件夹。 就我而言，它是 94.0.992.31。

3. 
接下来，双击并打开 Installer 文件夹。在文件夹中包含 msedge_7z.data 和 setup.exe 文件。

4. 
我们稍后将使用 setup.exe 文件卸载 Microsoft Edge。 在当前文件夹按住 Shift 键不放，单击右键，选择在 Windows 终端中打开。

5. 
当 PowerShell 窗口打开时，键入以下命令并按 Enter。 一段时间后，Microsoft Edge 将从您的系统中删除并卸载。


```
.\setup.exe -uninstall -system-level -verbose-logging -force-uninstall
```

通过以上命令我们就可以强制卸载掉 Windows 系统中预装的 Microsoft Edge 浏览器，以后无论何时需要安装 Microsoft Edge，都可以从 [https://www.microsoft.com/zh-cn/edge](https://www.microsoft.com/zh-cn/edge) 下载安装即可。

> 注意：

> 您的电脑中可能存在多个不同版本的 Microsoft Edge 浏览器，您可以在不同版本文件夹中找到 Installer 文件夹执行相同的命令卸载即可。


## 转载说明

[在 Windows 11/10 中卸载 Microsoft Edge 浏览器的简便方法](http://www.dayanzai.me/uninstall-microsoft-edge.html)
