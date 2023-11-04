---
layout: post
title: "在 Debian（Nginx）下構建 Webdav"
date: 2019-08-22 00:00:00
---

## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

本文介紹瞭如何在 `Debian` 環境中構建 `Webdav` 服務。
## 安裝Nginx

安裝 `Nginx` 服務

```
apt-get install nginx
```

## 配置 `Nginx`

刪除默認文件夾並創建用於用戶操作的 `Webdav` 目錄

```
rm -rf /var/www/html
mkdir -p /var/www/webdav
```

更改目錄所有者

```
chmod -R 755 /var/www/webdav
```

創建虛擬主機配置文件

```
/etc/nginx/sites-enabled/webdav.conf
```

內容如下：

```
server {
	listen 80;
	listen 443 ssl http2;
	server_name localhost;
	if ($ssl_protocol = "") { return 301 https://$host$request_uri; }
	#ssl on;
	ssl_certificate /etc/nginx/ssl/ssl.crt;
	ssl_certificate_key /etc/nginx/ssl/ssl.key;
	ssl_session_timeout 5m;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
	ssl_prefer_server_ciphers on;
	autoindex on;
	autoindex_exact_size off;
	autoindex_localtime on;
	charset utf-8,gbk;
	location / {
	root /var/www/webdav;
	autoindex on;
	dav_methods PUT DELETE MKCOL COPY MOVE;
	dav_ext_methods PROPFIND OPTIONS;
	create_full_put_path on;
	dav_access user:rw group:r all:r;
	auth_basic "Authorized Users Only";
	auth_basic_user_file /etc/nginx/.htpasswd;
	}
}
```

安裝 `htpasswd`

```
apt-get install apache2-utils
```

創建一個 `WebdAV` 密碼文件 `.htpasswd`

```
htpasswd -c /etc/nginx/.htpasswd username #username是想自定義的用戶名
```

使配置生效

```
service nginx restart
```

## 測試 `WebDAV`

使用瀏覽器訪問： https://IP_or_domain

此時，`Nginx` 已成功打開 `WebDAV` ！

Enjoy
