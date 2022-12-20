# GeoIP:如何从 IP 地址获取国家/城市/ASN

> 原文：<https://dev.to/nabbisen/geoip-how-to-get-country-city-asn-from-ip-address-2ce5>

## 简介

我发现我的服务器收到了几个未知主机的请求。我试图找出他们是谁。
他们的位置是一个很好的信息，是通过这篇文章中所写的方式获得的。
[GeoIP](https://dev.maxmind.com/geoip/legacy/downloadable/)by[MaxMind](https://www.maxmind.com/en/)做得很棒。

## 准备:[曼哈罗](https://manjaro.org/) / [Arch Linux](https://www.archlinux.org/)

通过 Manjaro 包管理器 [Pamac](https://wiki.manjaro.org/index.php/Pamac) 安装 3 个包:

*   geoip
*   地理信息数据库
    *   关于国家
*   GeoIP-数据库-附加
    *   关于城市/ASN，[自治系统](https://en.wikipedia.org/wiki/Autonomous_system_(Internet))编号

[AUR](https://aur.archlinux.org/) 也有。

此外，其他发行版的安装在这里是。

## 如何得到它

这是一项非常简单的任务。
就用`geoiplookup` :

```
$  geoiplookup %ip-or-fqdn%
GeoIP Country Edition: %Country Code%, %Country Name%
GeoIP City Edition, Rev 1: %Country Code%, ..., ..., ..., ..., %Latitude%, %Longitude%, ..., ...
GeoIP ASNum Edition: ... 
```

Enter fullscreen mode Exit fullscreen mode

快乐计算:)