---
layout: post
title: "Debian + Cloudreve 建立一个个人网盘"
date: 2021-09-06 00:00:00
---

## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

Cloudreve 可以让您快速搭建起公私兼备的网盘系统。Cloudreve 在底层支持不同的云存储平台，用户在实际使用时无须关心物理存储方式。你可以使用 Cloudreve 搭建个人用网盘、文件分享系统，亦或是针对大小团体的公有云系统。
项目GitHub地址：[https://github.com/cloudreve/Cloudreve](https://github.com/cloudreve/Cloudreve)

## 获取 Cloudreve

你可以在 [GitHub Release](https://github.com/cloudreve/Cloudreve/releases) 页面获取已经构建打包完成的主程序。其中每个版本都提供了常见系统架构下可用的主程序，命名规则为`cloudreve_版本号_操作系统_CPU架构.tar.gz` 。比如，普通64位Linux系统上部署3.0.0版本，则应该下载`cloudreve_3.0.0_linux_amd64.tar.gz`。

```
# 下载cloudreve主程序，wget后面是刚刚复制的地址
wget https://github.com/cloudreve/Cloudreve/releases/download/3.3.2/cloudreve_3.3.2_linux_amd64.tar.gz

# 解压
tar xvf cloudreve_3.3.2_linux_amd64.tar.gz

# 复制到指定位置
cp cloudreve /usr/bin/cloudreve

# 给予可执行权限
chmod 755 /usr/bin/cloudreve
```

## 安装 Nginx，Aria2，Mariadb

```
apt update
apt install aria2 nginx mariadb-server
```

因为要使用离线下载功能，为了获得更好的性能这里安装了mariadb替代了默认使用的sqlite。

安装 certbot，反代的时候用于签发 SSL 证书：

```
apt -y install snapd
snap install core
snap refresh core
snap install --classic certbot
ln -s /snap/bin/certbot /usr/bin/certbot
```

启动需要用到的服务

```
systemctl enable --now nginx mariadb
```

初始化数据库

```
mysql_secure_installation
```

创建一个名为cloudreve的数据库和用户：

```
mysql -u root -p
CREATE DATABASE cloudreve CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
GRANT ALL PRIVILEGES ON cloudreve.* TO cloudreve@localhost IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
quit
```

mysql默认运行在3306端口

## 配置cloudreve

新建一个文件：

```
nano /etc/cloudreve.conf
```

写入下面的内容：

```
[Database]
; 数据库类型，目前支持 sqlite | mysql
Type = mysql

; MySQL 端口
Port = 3306

; 用户名,根据自己情况修改
User = root

; 密码,根据自己情况修改
Password = mysql的root密码

; 数据库地址,根据自己情况修改
Host = 127.0.0.1

; 数据库名称,根据自己情况修改
Name = mysql

; 数据表前缀
TablePrefix = cd
```

### 进行测试

这个时候cloudreve服务就已经配置好了

如果你选用MySQL方式启动，在配置完毕后（选择sqlite则无需配置），需要启动一次来记录管理员用户名和密码，方便日后管理。

```
# 用刚刚创建的配置文件启动
cloudreve -c /etc/cloudreve.conf
```

可以看到终端里输出的信息，初始的账户和密码

![](/images/2021/09/cloudreve/cloudreve-1.png#alt=cloudreve-1)

它运行在5212端口，打开浏览器，输入你的服务器”公网ip:5212”，回车

输入刚刚显示的账户和密码就可以登陆了：

![](/images/2021/09/cloudreve/cloudreve-2.png#alt=cloudreve-2)

至此初步设置完毕

## 配置cloudreve开机启动

创建一个文件夹用来储存cloudreve的数据和文件

```
mkdir /home/cloudreve
```

编辑 `/usr/lib/systemd/system/cloudreve.service`

```
[Unit]
Description=Cloudreve
Documentation=https://docs.cloudreve.org
After=network.target
Wants=network.target

[Service]
# cloudreve 文件的存放路径,跟上面的一样，请根据自己的情况设置，默认储存策略的文件会储存在其中
WorkingDirectory=/home/cloudreve

ExecStart=/usr/bin/cloudreve -c /etc/cloudreve.conf

Restart=on-abnormal
RestartSec=5s
KillMode=mixed

StandardOutput=null
StandardError=syslog

[Install]
WantedBy=multi-user.target
```

启动cloudreve并设置开机自启：

```
systemctl enable --now cloudreve
```

```
如果你想取消开机启动，可以执行
systemctl disable cloudreve

如果你想查看cloudreve的运行状态
systemctl status cloudreve
```

## 配置反向代理和SSL

现在需要配置反向代理，新建nginx站点配置文件：

```
nano /etc/nginx/conf.d/cloudreve.conf
```

写入如下配置：

```
server {
    listen	80;
    server_name	disk.XXX.COM;
    client_max_body_size 0;

    location / {
        proxy_pass http://127.0.0.1:5212;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
    }
}
```

使用certbot签发ssl证书：

```
certbot --nginx
```

## 配置离线下载

创建需要用到的目录：

```
mkdir -p /home/cloudreve/aria2/{config,temp}
```

新建aria2c配置文件：

```
nano /home/cloudreve/aria2/config/aria2.conf
```

写入如下配置：

```
enable-rpc=true
rpc-listen-port=6800
rpc-secret=password
save-session-interval=1
save-session=/home/cloudreve/aria2/config/aria2.session
auto-save-interval=30
max-concurrent-downloads=100
max-connection-per-server=16
split=64
enable-dht=true
dht-file-path=/home/cloudreve/aria2/config/dht.dat
enable-peer-exchange=true
bt-enable-lpd=true
bt-max-peers=0
seed-time=0
```

新建systemd服务，用于守护aria2c进程：

```
nano /usr/lib/systemd/system/aria2c.service
```

写入如下配置：

```
[Unit]
Description=Aria2c Download Manager
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/aria2c --conf-path=/home/cloudreve/aria2/config/aria2.conf
Restart=on-abnormal
StandardOutput=null
StandardError=syslog

[Install]
WantedBy=multi-user.target
```

启动aria2c并设置开机自启：

```
systemctl enable --now aria2c
```

```
如果你想取消开机启动，可以执行
systemctl disable aria2c

如果你想查看cloudreve的运行状态
systemctl status aria2c
```

登录管理员后台，在离线下载这里配置连接：

![](/images/2021/09/cloudreve/cloudreve-3.png#alt=cloudreve-3)

点击保存，并点击测试连接，如果提示连接成功，Aria2 版本为：1.34.0，则正常

## 参考文献

- [https://zhujitips.com/3155](https://zhujitips.com/3155)
- [https://lingyunfx.com/2021/05/17/cloudreve-install/](https://lingyunfx.com/2021/05/17/cloudreve-install/)
- [https://lala.im/7842.html](https://lala.im/7842.html)
- [https://docs.cloudreve.org/getting-started/install](https://docs.cloudreve.org/getting-started/install)
- [https://www.daimajiaoliu.com/daima/712182259ec1c05](https://www.daimajiaoliu.com/daima/712182259ec1c05)
- [https://blog.m-jay.cn/?p=52](https://blog.m-jay.cn/?p=52)
- [https://hostloc.com/thread-888781-1-1.html](https://hostloc.com/thread-888781-1-1.html)

Enjoy
