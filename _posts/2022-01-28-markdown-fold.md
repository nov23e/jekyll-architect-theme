---
layout: post
title: "Markdown代码折叠与收起"
date: 2022-01-28 00:00:00
---

## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

有的时候，代码太长，全部展开显得很臃肿，所以我们可以采用代码折叠的方式。
Markdown很方便，但基本语法有些不足：比如无法使用折叠语法，无法让文字有不同的颜色。

这些功能可以实现，不过需要使用 html 语法进行扩展。这篇文章主要是整理一下这些技巧，方便更好的使用。

## 代码折叠

折叠语法：`<details>` 标签

```
<details>
  <summary>点击时的区域标题：点击查看详细内容</summary>
  <p> - 测试 测试测试</p>
  
<pre><code>title，value，callBack可以缺省</code></pre>
</details>
```

`details`：折叠语法标签

`summary`：折叠语法展示的摘要

`pre`：以原有格式显示元素内的文字是已经格式化的文本

`code`：指定代码范例

`blockcode`：表示程序的代码块

```
<details>
  <summary>点击时的区域标题</summary>
```

echo "hello shell"

echo "hello python"

```
</details>
```

## 效果展示

 - 测试 测试测试
```
title，value，callBack可以缺省
```


## 参考文档

- [https://guoflight.github.io/posts/28189/](https://guoflight.github.io/posts/28189/)
- [https://www.cnblogs.com/buwuliao/p/9578918.html](https://www.cnblogs.com/buwuliao/p/9578918.html)
- [https://www.gmlyo.com/2019/06/16/Markdown代码折叠与收起/](https://www.gmlyo.com/2019/06/16/Markdown%E4%BB%A3%E7%A0%81%E6%8A%98%E5%8F%A0%E4%B8%8E%E6%94%B6%E8%B5%B7/)

Enjoy!
