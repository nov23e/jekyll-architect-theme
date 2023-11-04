---


title: "Debian 一键安装 Google BBR"

slug: "one-click-bbr"

date: "2018-10-12"

draft: false

tags: ["BBR", "Debian"]

author: "Secy"

## contentCopyright: '[Creative Commons Attribution-ShareAlike License](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)'

`Google BBR` 是 `Google` 提出的一種新的擁塞控制算法,可使 `Linux` 服務器顯著提高吞吐量並減少 `TCP` 連接延遲.
## 開源地址

[https://github.com/google/bbr](https://github.com/google/bbr)

## 首先,讓我們談談開放方法.實際上,打開非常簡單,只需三行命令：

```
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p
```

如果正常執行後沒有錯誤,則需要重新啟動系統以使配置生效.

重啟後,執行以下命令,檢查配置是否成功生效：

```
root@cy:~# lsmod | grep bbr
tcp_bbr	20480  0
```

如果輸出與上述類似,則表明 `Google BBR` 配置成功.

Enjoy
