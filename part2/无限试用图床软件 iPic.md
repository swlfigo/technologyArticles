> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://imfht.github.io/15517711892880.html

在使用 Typora 时发现这个编辑器可以使用 iPic 上传图片。下载之后发现这个软件是订阅制的，需要 ￥60 / 年。  
iPic 确实是一款很好用的软件，支持的图床种类也有很多，但是作为一个几乎是 0 运营成本的软件我不能接受订阅制这个东西。

无限试用
----

默认 iPic 支持试用 7 天无限制图床，试用采取的是本地验证。试用开启时间存储在

```shell
/Users/yourname/Library/Containers/net.toolinbox.ipic/Data/Library/SyncedPreferences/net.toolinbox.ipic.plist
```

将 `values` -> `Markdown` -> `value` 更换为七天之内的时间戳即可实现无限试用。  
![](http://sylarimage.oss-cn-shenzhen.aliyuncs.com/2019-07-08-145431.png)

写一个小脚本
------

一个方便的做法是在启动 iPic 前将这个试用时间调到一个最近的时间。但将 `/Applications/iPic.app/Contents/MacOS/iPic`改为启动脚本会出现一些签名的奇怪问题。  
所以我的解决方法是

```shell
mkdir -p /Applications/cipc.app/MacOS/
```

在 `/Applications/cipc.app/MacOS/cipc` 中填入如下内容

```shell
!/bin/bash
/usr/libexec/PlistBuddy -c "Set ':values:Markdown Flag:value' `date +%s`" /Users/`whoami`/Library/Containers/net.toolinbox.ipic/Data/Library/SyncedPreferences/net.toolinbox.ipic.plist && nohup /Applications/iPic.app/Contents/MacOS/iPic &
```

别忘了添加可执行权限 `chmod +x /Applications/cipc.app/MacOS/cipc`，  
然后运行 cipc 即可实现对 iPic 的无限试用。