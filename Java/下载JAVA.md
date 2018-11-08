---
title : 如何在Linux环境下载JDK
flag : java 下载 JDk
time : 2018/10/30
---


# 如何在Linux环境下载JDK

1. 前提
[JDK下载网址](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
2. 问题
在浏览器点击下载链接，可以正常下载，但是在Linux环境下，使用wget下载时，返回数据很少，且返回的数据格式是html的。如下：
```shell
shell# wget --spider http://download.oracle.com/otn-pub/java/jdk/11.0.1+13/90cf5d8f270a4347a95050320eef3fb7/jdk-11.0.1_linux-x64_bin.rpm
Spider mode enabled. Check if remote file exists.
--2018-10-23 15:29:34--  http://download.oracle.com/otn-pub/java/jdk/11.0.1+13/90cf5d8f270a4347a95050320eef3fb7/jdk-11.0.1_linux-x64_bin.rpm
Resolving download.oracle.com (download.oracle.com)... 104.118.31.125
Connecting to download.oracle.com (download.oracle.com)|104.118.31.125|:80... connected.
HTTP request sent, awaiting response... 302 Moved Temporarily
Location: https://edelivery.oracle.com/otn-pub/java/jdk/11.0.1+13/90cf5d8f270a4347a95050320eef3fb7/jdk-11.0.1_linux-x64_bin.rpm [following]
Spider mode enabled. Check if remote file exists.
--2018-10-23 15:29:34--  https://edelivery.oracle.com/otn-pub/java/jdk/11.0.1+13/90cf5d8f270a4347a95050320eef3fb7/jdk-11.0.1_linux-x64_bin.rpm
Resolving edelivery.oracle.com (edelivery.oracle.com)... 23.47.129.179, 2600:140b:13:2af::2d3e, 2600:140b:13:291::2d3e
Connecting to edelivery.oracle.com (edelivery.oracle.com)|23.47.129.179|:443... connected.
HTTP request sent, awaiting response... 302 Moved Temporarily
Location: http://download.oracle.com/errors/download-fail-1505220.html [following]
Spider mode enabled. Check if remote file exists.
--2018-10-23 15:29:36--  http://download.oracle.com/errors/download-fail-1505220.html
Connecting to download.oracle.com (download.oracle.com)|104.118.31.125|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 5307 (5.2K) [text/html]
Remote file exists and could contain further links,
but recursion is disabled -- not retrieving.
```

3. 解决方案
原因是下载JDK是需要接受Oracle官方的许可协议，而使用wget直接下载，Oracle后端是没有获取到许可协议相关的信息的。所以，可以如下操作：
```shell
shell# wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u192-b12/750e1c8617c5452694857ad95c3ee230/jdk-8u192-linux-x64.tar.gz

```


