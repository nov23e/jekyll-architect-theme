---
layout: post
title: "建立一个Hexo个人独立博客并备份"
date: 2021-10-04 00:00:00
---


## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

`Hexo` 是一个快速,简洁,高效的博客框架.
`Hexo` 使用 `Markdown` 解析文章,并在几秒钟内就可以生成带有精美主题的静态网页.

## 准备

- Windows 10 企业版LTSC
- [Node.JS 14.18.0 LTS](https://nodejs.org/dist/v14.18.0/node-v14.18.0-x64.msi)
- [Git 2.33.0](https://github.com/git-for-windows/git/releases/download/v2.33.0.windows.2/Git-2.33.0.2-64-bit.exe)

### Node.JS 安装

官方推荐安装 `Node.Js` 的方法：[https://github.com/nodesource/distributions/blob/master/README.md](https://github.com/nodesource/distributions/blob/master/README.md)

查看 `Node.js` 版本

```
node --version
```

查看 `NPM` 版本

```
npm --version
```

## 将您的博客部署到 `GitHub` 并且备份

如果您没有要先注册的 `GitHub` 帐户,则直接转到 `GitHub` 帐户.

设置 `user.name` 和 `user.email`

将以下命令中的“Your user.name”和“You user.email”替换为您自己的

```
git config --global user.name "Your user.name"
git config --global user.email "You user.email"
```

生成 `ssh` 密钥

`You user.email` 是您自己注册 `GitHub` 的邮箱

```
ssh-keygen -t rsa -C "You user.email"
```

输入保存到下面的路径

```
/c/Users/XXX/.ssh/id_rsa
```

然后只需按`Enter`.

查看去文件夹里查看 `id_rsa.pub` 的密钥

### 在 `GitHub` 帳戶下添加SSH密鑰

打开`Github`的 `Settings`，选择 [SSH and GPG keys](https://github.com/settings/keys) ，在 `New SSH key` 标题输入 `blog` ，内容就输入刚刚生成的密钥点击保存

### 创建 `GitHub` 存储卡

命名格式为“帐户的用户名” .github.io

例如：`userName.github.io` 创建 `GitHub` 存储库，并且创建一个 `gh-pages` 分支

如果不是以这种格式创建资源库，则在创建域绑定后,需要转到 `Github` 项目中的“设置”,选择 `GitHub Pages` 在 `Source` 中选择 `main` 分支,然后单击 `Save` .

### 把仓库从`Github`下载到本地

```
git clone https://github.com/yourgithubname/yourgithubname.github.io.git
```

## 安裝 `Hexo`

在电脑上创建 `yourgithubname.github.io` 文件夾

在Git里执行命令让 `NPM` 使用 `SOCKS5` ，没有就算了

```
git config --global http.proxy 'socks5://127.0.0.1:1080'
git config --global https.proxy 'socks5://127.0.0.1:1080'
```

使用命令安裝 `Hexo`

```
npm install hexo-cli -g
```

查看 `Hexo` 版本号

```
hexo -v
```

初始化 `Hexo`

```
hexo init
```

测试安装成功

### 测试 `Hexo` 服務

```
hexo s
```

打开浏览器访问 `http://localhost:4000` 查看成功的博客页面

### 修改 `Hexo` 配置文件

打开 `Hexo` 文件

```
_config.yml
```

修改相应的部分

```
deploy:
  type: git
  repo: git@github.com:(BoView)/(BoView).github.io.git
  branch: gh-pages
```

保存

### 测试和部署

清除静态頁面

```
hexo clean
```

生成静态頁面

```
hexo g
```

将公共文件内容部署到 `Github` 存储库

```
hexo d
```

如果部署遇到错误,请首先运行以下命令

```
npm install hexo-deployer-git --save
```

然后重新部署

```
hexo d
```

### 打开 `Hexo` 博客

此时,您可以打开浏览器访问 `userName.github.io` （GitHub存储库名称）以访问构建的博客页面.

## 下面是备份换节不需要的可以跳过

### 建立.gitignore

建立`.gitignore`文件将不需要备份的文件屏蔽。个人的`.gitignore`文件如下：

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

### 在Github上备份

通过如下命令将本地文件备份到Github上。

假设目前在`hexo`博客的根目录下。

```
$ git add .
$ git commit -m "Backup"
$ git push -u origin main
```

这样就备份完博客了且在Github上能看到两个分支(main和gh-pages)。

PS.将themes/XXX/中的`.git/`删除，否则无法将主题文件夹push；

### 个人备份习惯

个人而言习惯于先备份文件再生成博客。即先执行`git add .,git commit -m "Backup",git push origin main`将博客备份完成，然后执行`hexo g -d`发布博客。

### 恢复博客

目前假设本地`Hexo`博客基础环境已经搭好。

输入下列命令克隆博客必须文件(main分支)到本地：

```
$ git clone https://github.com/yourgithubname/yourgithubname.github.io
```

恢复博客

在克隆的那个文件夹下输入如下命令恢复博客：

```
$ npm install hexo-cli -g
$ npm install
$ npm install hexo-deployer-git
```

在此不需要执行`hexo init`这条指令，因为不是从零搭建起新博客。

如果提示有模块丢失类似的错误，就先把`gh-pages`以前旧的生成博文清空再恢复

### 绑定域名

添加解析记录

如果要继续浏览域名,前提是要拥有自己的域名. 如果可以通过上述仓库名称访问它,则可以跳过此步骤.

转到您自己的域名,然后将解析记录类型添加到 `CNAME` . 主机记录是 `@CNAME` 选择默认值, `TTL` 选择是 `600` ,记录值是 `github` 的存储库名称 `userName.github.io`

配置 `Hexo` 文件

创建一个 `CNAME` 配置文件

使用 `hexo d` 命令部署时，实际上是将 `~/Documents/blog/public` 文件夹的内容 push 到 [username].github.io 上，这样会把 `CNAME` 文件覆盖掉，而把 `CNAME` 文件添加到 `source` 文件夹里，可以避免这种情况。

```
touch ~/Documents/source/CNAME
```

在 `CNAME` 文件下添加刚解析的域名.

然后重新部署

```
hexo g
hexo d
```

## `Hexo` 博客已建立

恭喜啦

此时,打开浏览器访问自己的域名,就可以访问自己的博客,打开您自己的博客之旅.

## 错误提示

推送文件到`Github`提示`Not a git repository (or any of the parent directories): .git`

```
git init
```

## 附录

Hexo的源文件说明：

1. `_config.yml`站点的配置文件，需要拷贝；
2. `themes/`主题文件夹，需要拷贝；
3. `source`博客文章的.md文件，需要拷贝；
4. `scaffolds/`文章的模板，需要拷贝；
5. `package.json`安装包的名称，需要拷贝；
6. `.gitignore`限定在push时哪些文件可以忽略，需要拷贝；
7. `.git/`主题和站点都有，标志这是一个git项目，不需要拷贝；
8. `node_modules/`是安装包的目录，在执行npm install的时候会重新生成，不需要拷贝；
9. `public`是hexo g生成的静态网页，不需要拷贝；
10. `.deploy_git`同上，hexo g也会生成，不需要拷贝；
11. `db.json`文件，不需要拷贝。

不需要拷贝的文件正是.gitignore中所忽略的。

## 参考说明

- [Hexo | 怎么去备份你的Hexo博客](https://www.jianshu.com/p/baab04284923)
- [在Github上备份Hexo博客](https://lrscy.github.io/2018/01/26/Hexo-Github-Backup/)
- [使用GitHub备份Hexo博客源文件](https://zhuanlan.zhihu.com/p/127027266)
- [Git socks5 代理设置](https://blog.csdn.net/huangkangying/article/details/78858446)
- [fatal: Not a git repository (or any of the parent directories): .git [duplicate]](https://stackoverflow.com/questions/20413459/fatal-not-a-git-repository-or-any-of-the-parent-directories-git)
- [为github帐号添加SSH keys](https://blog.csdn.net/so_geili/article/details/62041664)
- [hexo 如何智能备份](https://www.v2ex.com/t/138722)
- [怎么备份 hexo 博客？](https://www.v2ex.com/t/214603)
- [为 Github 上的 Hexo 博客绑定个性域名](https://cps.ninja/2016/10/09/customize-your-blog-domain/)

Enjoy
