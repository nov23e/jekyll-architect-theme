---
layout: post
title: "Debian/Ubuntu 中安裝和配置 UFW"
date: 2020-01-17 00:00:00
---

## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

UFW 是一個 Arch Linux、Debian 或 Ubuntu 中管理防火牆規則的前端，可大大簡化防火牆的配置過程.
## 安裝 UFW

如還沒有安裝，可以使用 apt 命令來安裝

```
apt-get update && apt-get install ufw
```

在使用前，你應該檢查下 UFW 是否已經在運行.

```
ufw status
```

如果你發現狀態是 `inactive` ，意思是沒有被激活或不起作用.

## 啟用 IPv6 支持

如果服務器已啟用 `IPv6` ，請確保將 `UFW` 配置為支持 `IPv6` ，以便除 `IPv4` 之外還管理 `IPv6` 的防火牆規則.使用如下命令修改 `UFW` 的配置文件：

```
nano /etc/default/ufw
```

將`IPV6=no`修改為`IPV6=yes`,保存並退出.

## 啟用/禁用

```
ufw enable #啟用
ufw disable #禁用
```

## 使用與配置

### 列出當前UFW規則

```
ufw status verbose
```

## 添加規則

### 允許入站（allow）

默認情況，沒有允許就是拒絕（入站），使用 `ufw allow <端口>` 來添加允許訪問的端口或協議.

```
ufw allow ssh #添加22端口
ufw allow http #添加80端口
ufw allow https #添加443端口
ufw allow 4000/tcp #添加4000端口，僅TCP協議
ufw allow 4000/udp #添加4000端口，僅UDP協議
ufw allow 8888:9999 #添加8888到9999之間的端口
```

### 拒絕訪問（deny）

使用 `ufw deny <端口>` 來添加拒絕入站的端和協議，與添加允許的類似.

## 刪除規則

先使用 `ufw status` 查看規則，再使用 ufw delete [規則] <端口> 來刪除規則.

```
ufw delete allow 2333/tcp
```

如果你有很多條規則，使用 `numbered` 參數，可以在每條規則上加個序號數字.

然後使用 `ufw delete <序號>` 來刪除規則.

```
root@CY:~# ufw status numbered #列出規則，並加上序號.
Status: active

     To Action From
     -- ------ ----
[ 1] 20,21,22,80,888,8888/tcp ALLOW IN Anywhere
[ 2] 39000:40000/tcp ALLOW IN Anywhere
[ 3] 8896/tcp ALLOW IN Anywhere
[ 4] 8896/udp ALLOW IN Anywhere
[ 5] 443/tcp ALLOW IN Anywhere
[ 6] 20,21,22,80,888,8888/tcp (v6) ALLOW IN Anywhere (v6)
[ 7] 39000:40000/tcp (v6) ALLOW IN Anywhere (v6)
[ 8] 8896/tcp (v6) ALLOW IN Anywhere (v6)
[ 9] 8896/udp (v6) ALLOW IN Anywhere (v6)
[10] 443/tcp (v6) ALLOW IN Anywhere (v6)

root@CY:~# ufw delete 4 #刪除上面的第4條規則
Deleting:
 allow 8896/udp
Proceed with operation (y|n)? y #最後會詢問你是否進行操作
```

以上都是一些簡單常用的一些命令，想要深入了解，可以輸入 `man ufw` 查看 ufw 用戶手冊.

Enjoy
