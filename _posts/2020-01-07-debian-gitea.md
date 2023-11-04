---
layout: post
title: "在 Debian 上安裝 Gitea"
date: 2020-01-07 00:00:00
---

## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

`Gitea` 是 `Git` 支持的另一個開源自託管版本控制系統, `Gitea` 是用 `Golang` 編寫的是一個可以在任何平台上託管的輕量級解決方案.
前提條件

- 新建Debian 9實例
 - 有sudo權限的非root用戶
 - Nginx
 - Git
 - MariaDB

## 安裝 `Nginx`

更新軟件包列表

```
apt update
```

安裝Nginx

```
apt -y install nginx
```

安裝完成後,運行以下命令啟動和啟用 `Nginx` 服務

```
systemctl enable nginx.service
systemctl start nginx.service
```

## 安裝 `Git`

```
apt -y install git
```

## 安裝 `MariaDB` 數據庫服務器

`Gitea` 支持以下數據庫服務器

- MariaDB/mysql
 - PostgreSQL
 - SQLite
 - TiDB

對於本教程,我們會使用 `MariaDB` 服務器和客戶端

```
apt -y install mariadb-server mariadb-client
```

完成後,確保 `MariaDB` 已啟用,並且正在運行

```
systemctl enable mariadb.service
systemctl start mariadb.service
```

然後,運行下面的命令以保護 `MariaDB` 服務器,創建 `root` 密碼,並且禁止遠程 `root` 訪問

```
mysql_secure_installation
```

出現提示時,請按照下列指南迴答下列問題

```
Enter current password for root (enter for none): Just press the Enter
Set root password? [Y/n]: Y
New password: Enter password
Re-enter new password: Repeat password
Remove anonymous users? [Y/n]: Y
Disallow root login remotely? [Y/n]: Y
Remove test database and access to it? [Y/n]: Y
Reload privilege tables now? [Y/n]: Y
```

重新啟動 `MariaDB`

```
systemctl restart mariadb.service
```

鍵入下面的命令登錄到 `MariaDB` 控制台

```
mysql -u root -p
```

然後鍵入你在上面創建的密碼以登錄,你將看到 `MariaDB` 歡迎消息

創建一個名為 `Gitea` 的數據庫

```
CREATE DATABASE gitea;
```

創建一個叫 `Gitea` 的數據庫用戶,使用新密碼

```
CREATE USER 'gitea'@'localhost' IDENTIFIED BY 'new_password_here';
```

確保你用一個強大而復雜的密碼替換 `new_password_here`

然後授予用戶對數據庫的完全訪問權限

```
GRANT ALL ON gitea.* TO 'gitea'@'localhost' IDENTIFIED BY 'user_password_here' WITH GRANT OPTION;
```

最後,保存更改並退出

```
FLUSH PRIVILEGES;
EXIT;
```

## 準備 `Gitea` 環境

創建用戶以運行 `Gitea`

```
adduser --system --shell /bin/bash --gecos 'Git Version Control' --group --disabled-password --home /home/git git
```

創建所需的目錄結構

```
mkdir -p /var/lib/gitea/{custom,data,indexers,public,log}
chown git:git /var/lib/gitea/{data,indexers,log}
chmod 750 /var/lib/gitea/{data,indexers,log}
mkdir /etc/gitea
chown root:git /etc/gitea
chmod 770 /etc/gitea
```

## 安裝 `Gitea`

可以通過運行以下命令下載 `Gitea` 二進製文件

```
wget -O gitea https://dl.gitea.io/gitea/1.10.2/gitea-1.10.2-linux-amd64
chmod +x gitea
```

將二進製文件複製到全局位置.

```
cp gitea /usr/local/bin/gitea
```

## 創建一個服務文件來自動啟動 `Gitea`

創建 `Linux` 服務文件

```
touch /etc/systemd/system/gitea.service
```

如果使用你選擇的文本編輯器,打開這個新創建的文件,並且在以下情況下填充

```
[Unit]
Description=Gitea (Git with a cup of tea)
After=syslog.target
After=network.target
After=mariadb.service

[Service]
# Modify these two values and uncomment them if you have
# repos with lots of files and get an HTTP error 500 because
# of that
###
#LimitMEMLOCK=infinity
#LimitNOFILE=65535
RestartSec=2s
Type=simple
User=git
Group=git
WorkingDirectory=/var/lib/gitea/
ExecStart=/usr/local/bin/gitea web -c /etc/gitea/app.ini
Restart=always
Environment=USER=git HOME=/home/git GITEA_WORK_DIR=/var/lib/gitea
# If you want to bind Gitea to a port below 1024 uncomment
# the two values below
###
#CapabilityBoundingSet=CAP_NET_BIND_SERVICE
#AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target
```

啟動時啟用並啟動 `Gitea`

```
systemctl daemon-reload
systemctl enable gitea
systemctl start gitea
```

確保 `Gitea` 正在運行

```
systemctl status gitea
```

## 將 `Nginx` 配置為反向代理

刪除默認的 `Nginx` 配置文件

```
rm /etc/nginx/sites-enabled/default
```

為 `Gitea` 創建反向代理配置

```
touch /etc/nginx/sites-available/gitea
```

使用下列配置填充文件,確保將 `example.com` 替換為域名或 `IP` 地址.

```
upstream gitea {
	server 127.0.0.1:3000;
}

server {
	listen 80 default_server;
	listen 443 ssl http2;
	client_max_body_size 0;
	server_name example.com;
	if ($server_port !~ 443){
		rewrite ^(/.*)$ https://$host$1 permanent;
	}
	
	root /var/lib/gitea/public;
	access_log off;
	error_log off;
	ssl_certificate    /etc/nginx/ssl/ssl.crt;
	ssl_certificate_key    /etc/nginx/ssl/ssl.key;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
	ssl_prefer_server_ciphers on;
	ssl_session_cache shared:SSL:10m;
	ssl_session_timeout 10m;
	error_page 497  https://$host$request_uri;

	location / {
	proxy_pass http://localhost:3000;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	proxy_set_header X-Real-IP $remote_addr;
	proxy_set_header Host $http_host;
	proxy_set_header X-Forwarded-Proto $scheme;
	proxy_max_temp_file_size 0;
	proxy_redirect off;
	proxy_read_timeout 120;
	}
}
```

啟用 `Gitea nginx` 反向代理配置.

```
ln -s /etc/nginx/sites-available/gitea /etc/nginx/sites-enabled/gitea
```

然後重新加載 `Nginx` 服務

```
systemctl reload nginx.service
```

接下來,打開瀏覽器,並且瀏覽服務器主機名或 `IP` 地址

```
https://YOUR_SERVER_IP/install
```

按照屏幕上的說明完成 `Gitea` 設置

Enjoy
