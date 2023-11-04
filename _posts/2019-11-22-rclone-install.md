---
layout: post
title: "Rclone的安裝及掛載使用"
date: 2019-11-22 00:00:00
---

## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

`Rclone` 是一款命令行工具,支持在不同對象儲存,網盤之間上傳,下載以及同步數據.
## RClone項目官網

Rclone官網：[https://rclone.org](https://rclone.org)

GitHub項目：[https://github.com/ncw/rclone](https://github.com/ncw/rclone)

## 支持多種主流對象儲存

```
Amazon Drive   ([See note][3])
Amazon S3  
Backblaze B2  
Box  
Ceph  
DigitalOcean Spaces  
Dreamhost  
Dropbox  
FTP  
Google Cloud Storage  
Google Drive  
HTTP  
Hubic  
IBM COS S3  
Memset Memstore  
Mega  
Microsoft Azure Blob Storage  
Microsoft OneDrive  
Minio  
Nextcloud  
OVH  
OpenDrive  
Openstack Swift  
Oracle Cloud Storage  
ownCloud  
pCloud  
put.io  
QingStor  
Rackspace Cloud Files  
SFTP  
Wasabi  
WebDAV  
Yandex Disk  
The local filesystem
```

這裡我們以 `Google Drive` 為例

## 安裝

系統簡介

- Debian 10 x86_64
- Google Drive

安裝 `Rclone`

```
apt-get install curl
curl https://rclone.org/install.sh | bash
```

很簡單的就將其安裝完畢

配置 `Rclone`

```
rclone config
```

配置方案如下 手動操作的位置會加上註釋

```
No remotes found - make a new one
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n      #這裡輸入n表示新建一個配置
name> cy      #輸入自定義的配置名
Type of storage to configure.
Choose a number from below, or type in your own value
 1 / Alias for a existing remote
   \\ \"alias\"
 2 / Amazon Drive
   \\ \"amazon cloud drive\"
 3 / Amazon S3 Compliant Storage Providers (AWS, Ceph, Dreamhost, IBM COS, Minio)
   \\ \"s3\"
 4 / Backblaze B2
   \\ \"b2\"
 5 / Box
   \\ \"box\"
 6 / Cache a remote
   \\ \"cache\"
 7 / Dropbox
   \\ \"dropbox\"
 8 / Encrypt/Decrypt a remote
   \\ \"crypt\"
 9 / FTP Connection
   \\ \"ftp\"
10 / Google Cloud Storage (this is not Google Drive)
   \\ \"google cloud storage\"
11 / Google Drive
   \\ \"drive\"
12 / Hubic
   \\ \"hubic\"
13 / Local Disk
   \\ \"local\"
14 / Mega
   \\ \"mega\"
15 / Microsoft Azure Blob Storage
   \\ \"azureblob\"
16 / Microsoft OneDrive
   \\ \"onedrive\"
17 / OpenDrive
   \\ \"opendrive\"
18 / Openstack Swift (Rackspace Cloud Files, Memset Memstore, OVH)
   \\ \"swift\"
19 / Pcloud
   \\ \"pcloud\"
20 / QingCloud Object Storage
   \\ \"qingstor\"
21 / SSH/SFTP Connection
   \\ \"sftp\"
22 / Webdav
   \\ \"webdav\"
23 / Yandex Disk
   \\ \"yandex\"
24 / http Connection
   \\ \"http\"
Storage> 11      #注意：這裡的Google Cloud Storage不是Google Drive
Google Application Client Id - leave blank normally.
client_id>       #為了採用自動配置 接下來幾個選項直接Enter
Google Application Client Secret - leave blank normally.
client_secret>       #Enter
Scope that rclone should use when requesting access from drive.
Choose a number from below, or type in your own value
 1 / Full access all files, excluding Application Data Folder.
   \\ \"drive\"
 2 / Read-only access to file metadata and file contents.
   \\ \"drive.readonly\"
   / Access to files created by rclone only.
 3 | These are visible in the drive website.
   | File authorization is revoked when the user deauthorizes the app.
   \\ \"drive.file\"
   / Allows read and write access to the Application Data folder.
 4 | This is not visible in the drive website.
   \\ \"drive.appfolder\"
   / Allows read-only access to file metadata but
 5 | does not allow any access to read or download file content.
   \\ \"drive.metadata.readonly\"
scope>       #Enter 
ID of the root folder - leave blank normally.  Fill in to access \"Computers\" folders. (see docs).
root_folder_id>       #Enter 
Service Account Credentials JSON file path  - leave blank normally.
Needed only if you want use SA instead of interactive login.
service_account_file>       #Enter 
Remote config
Use auto config?
 * Say Y if not sure
 * Say N if you are working on a remote or headless machine or Y didn\'t work
y) Yes
n) No
y/n> n      #輸入n表示採用自動配置
If your browser doesn\'t open automatically go to the following link: [Link]      #這裡會出現一長串的網址 將其複製到瀏覽器上並訪問 登陸後即可獲得一串[verification code]
Log in and authorize rclone for access
Enter verification code> [verification code]      #這裡輸入你獲取的[verification code]
Configure this as a team drive?
y) Yes
n) No
y/n> n      #選擇是否為團隊網盤
--------------------
[gd]
type = drive
client_id = 
client_secret = 
scope = 
root_folder_id = 
service_account_file = 
token = {[token]}
--------------------
y) Yes this is OK
e) Edit this remote
d) Delete this remote
y/e/d> y      #輸入y表示確認該配置
Current remotes:

Name                 Type
====                 ====
cy                   drive

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q      #輸入q退出配置過程
```

至此已經成功配置完成 `Rclone`

## 使用

`Rclone` 命令的語法格式

```
Syntax: [options] subcommand <parameters> <parameters...>
```

常用命令如下表

```
rclone config - 以控制會話的形式添加rclone的配置,配置保存在.rclone.conf文件中。
rclone copy - 將文件從源複製到目的地址,跳過已復製完成的。
rclone sync - 將源數據同步到目的地址,只更新目的地址的數據。
rclone move - 將源數據移動到目的地址。
rclone delete - 刪除指定路徑下的文件內容。
rclone purge - 清空指定路徑下所有文件數據。
rclone mkdir - 創建一個新目錄。
rclone rmdir - 刪除空目錄。
rclone check - 檢查源和目的地址數據是否匹配。
rclone ls - 列出指定路徑下所有的文件以及文件大小和路徑。
rclone lsd - 列出指定路徑下所有的目錄/容器/桶。
rclone lsl - 列出指定路徑下所有文件以及修改時間,文件大小和路徑。
rclone md5sum - 為指定路徑下的所有文件產生一個md5sum文件。
rclone sha1sum - 為指定路徑下的所有文件產生一個sha1sum文件。
rclone size - 獲取指定路徑下,文件內容的總大小。 .
rclone version - 查看當前版本。
rclone cleanup - 清空remote。
rclone dedupe - 交互式查找重複文件,進行刪除/重命名操作。
```

## 掛載到本地

創建一個掛載點

```
mkdir [path]      #[path]表示掛載點路徑 可自定義
```

接著將 `Drive` 掛載到 [path]

```
rclone mount [name]:/ [path] --allow-other --allow-non-empty --vfs-cache-mode writes
#[name]表示Drive的配置名 [path]表示掛載點路徑 可自定義 但必須存在該路徑
```

輸入完這條指令後就沒什麼動靜了 等幾分鐘後我們手動斷開和服務器的連接 再重新用 `SSH` 登錄就可以了

至此 我們已經成功掛載了 `Google Drive`

查看是否成功掛載

```
df -h
```

看到返回的值裡面出現了

```
Filesystem     Type         Size  Used Avail Use% Mounted on
udev           devtmpfs     463M     0  463M   0% /dev
tmpfs          tmpfs         99M  652K   98M   1% /run
/dev/vda1      ext4          25G  5.2G   19G  22% /
tmpfs          tmpfs        493M     0  493M   0% /dev/shm
tmpfs          tmpfs        5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs        493M     0  493M   0% /sys/fs/cgroup
tmpfs          tmpfs         99M     0   99M   0% /run/user/0
[name]:        fuse.rclone   50G   10G   50G  20% [path]      #Rclone挂载点
```

如果掛載報錯如下內容：

```
Fatal error: failed to mount FUSE fs: fusermount: exec: "fusermount": executable file not found in $PATH
```

執行如下命令重新掛載即可

```
apt-get install fuse
```

Enjoy
