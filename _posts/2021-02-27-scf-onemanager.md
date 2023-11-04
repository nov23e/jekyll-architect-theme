---
layout: post
title: "腾讯云SCF云函数无服务器部署OneDrive分享网盘"
date: 2021-02-27 00:00:00
---

## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

OneDrive的网盘程序介绍过很多款,但是都是需要VPS服务器来部署,本文将介绍无需服务器,使用腾讯云SCF云函数服务,即可在云端部署你的OneDrive分享云盘.
> 本文为 [Stille](https://www.ioiox.com/stille.html) 原创文章.经实践,测试,整理发布.如需转载请联系作者获得授权,并注明转载地址.


## 部署准备

### 获取API密钥

**登陆腾讯云控制台 - 访问管理**

API密钥管理 - 新建密钥

获取`SecretId`和`SecretKey`

![](/images/2021/02/onemanager/onemanager-1.jpg#alt=)

## 获取云函数代码

[![](https://github.githubassets.com//images/modules/site/icons/footer/github-logo.svg#alt=Deploy)](https://github.com/qkqpttgf/OneManager-php)

访问项目地址点击download下载并解压缩

## 部署SCF云函数

### 新建函数

**登陆腾讯云控制台 - SCF云函数**

区域选择 - 中国香港 - 新建

> 有热心网友在本文下留言测试过多个区域,除了香港成功,其他北京,上海,广州,成都均失败或者不支持,所以还是建议此处区域选择香港!


![](/images/2021/02/onemanager/onemanager-2.jpg#alt=)

填写函数名

运行环境选择`Php 7.2`

选择空白函数

下一步

![](/images/2021/02/onemanager/onemanager-3.jpg#alt=)

拉到下方`高级配置`

环境变量中分别添加上文获取的`SecretId`和`SecretKey`和Region

完成

![](/images/2021/02/onemanager/onemanager-4.jpg#alt=)

### 上传函数代码

**函数代码**

提交方法 - 本地上传文件夹

选择刚才解压缩的文件夹保存

![](/images/2021/02/onemanager/onemanager-5.jpg#alt=)

### 触发方式

**添加触发方式**

- 触发方式 - API网关触发器
- `勾选` 启用集成响应
- 其他保持默认并保存

![](/images/2021/02/onemanager/onemanager-6.jpg#alt=)

生成访问路径

![](/images/2021/02/onemanager/onemanager-7.jpg#alt=)

## 部署网盘

### 链接OneDrive

访问生成的访问路径

![](/images/2021/02/onemanager/onemanager-8.jpg#alt=)

选择你的OneDrive类型

![](/images/2021/02/onemanager/onemanager-9.jpg#alt=)

确认后会弹出登陆OneDrive页面,登陆后等待跳转回即可成功链接.

![](/images/2021/02/onemanager/onemanager-10.jpg#alt=)

## 配置网盘

**函数配置 - 编辑**

`为提高访问体验,可将内存可提高至512M或1024M`

连接过OneDrive后,此时的环境变量已经自动生成了一些配置,**`请不要删除`**.

**现在的版本升级后会自动添加到环境变量里，不需要手动配置**

~~**继续添加以下环境变量:**

- sitename => `随苑的盘`

配置站点名称
- public_path => `/`

私有地址访问网盘的文件展示路径,/为根目录.
- private_path => `/`

配置CDN加速域名访问网盘的文件展示路径,/为根目录.
- passfile => `.password`

配置加密功能,目录下有.password的文件,改目录需密码才能访问.
- admin => `xxxxxxxx`

配置管理员密码~~

> 需加密的文件夹中,创建或上传.password文件,并将密码填写至.password文件内.


![](/images/2021/02/onemanager/onemanager-11.jpg#alt=)

## 配置域名

### 解析专用域名

为本网盘配置一个二级域名,并解析CNAME到上文生成的`访问路径`的`前半部分`.

CNAME参考

`service-ft48dihy-1253567893.ap-hongkong.apigateway.myqcloud.com`

### 自定义域名

触发方式 - 点击API服务名

![](/images/2021/02/onemanager/onemanager-12.jpg#alt=)

自定义域名 - 新建

![](/images/2021/02/onemanager/onemanager-13.jpg#alt=)

新增自定义域名

- 填写自定义域名
- 协议选择`http和https`
- 选择证书,如没有可以`点击前往`免费申请.
- 路径映射选择自定义路径,值为`/`和`发布`.

![](/images/2021/02/onemanager/onemanager-14.jpg#alt=)

管理API - 点击编辑

![](/images/2021/02/onemanager/onemanager-15.jpg#alt=)

路径改为`/`根目录

下一步

![](/images/2021/02/onemanager/onemanager-16.jpg#alt=)

确保启用`响应集成`

下一步

![](/images/2021/02/onemanager/onemanager-17.jpg#alt=)

返回类型选择`HTML`

完成

![](/images/2021/02/onemanager/onemanager-18.jpg#alt=)

前往发布服务

![](/images/2021/02/onemanager/onemanager-19.jpg#alt=)

点击发布

![](/images/2021/02/onemanager/onemanager-20.jpg#alt=)

发布环境选择`发布`

备注自定义填写后提交

![](/images/2021/02/onemanager/onemanager-21.jpg#alt=)

### 配置强制跳转HTTPS

函数服务 - 函数代码

选择左侧`index.php`

找到`<head>`标签,在下面粘贴以下代码

强制跳转代码

```
<script>
  if (location.protocol === "http:") {
    location.href = location.href.replace(/http/, "https");
  }
</script>
```

> head标签大约在600多行,但随着OneDrive_SCF程序的更新,head所在行数可能有所变化,参照下图查找即可.


![](/images/2021/02/onemanager/onemanager-22.jpg#alt=)

## 部署完成

至此完成以上全部流程,现在可以使用域名来访问网盘了.

![](/images/2021/02/onemanager/onemanager-10.jpg#alt=)

## 结语

腾讯云SCF云函数服务在`2020年1月7日正式收费`,免费额度为每月40万GBs资源使用量及100万次跳用次数.而本网盘程序仅仅只是需要耗费调用OneDrive的API请求的资源使用量和次数,来获取文件的直连下载地址.最终上传和下载文件都是直连OneDrive服务器,不会产生额外流量费用,`所以免费的额度基本时足够日常使用了`.

Enjoy
