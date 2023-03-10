# 带 stunnel 的 RTMP/RTMPS 继电器

> 原文：<https://dev.to/lax/rtmps-relay-with-stunnel-12d3>

2019 年 5 月 1 日，脸书直播将[从直播 API 和 GoLive 页面弃用 RTMP](https://developers.facebook.com/docs/graph-api/changelog/breaking-changes/#live-api-4-24) ，并使用 RTMPS 强制执行。

* * *

我通过 [nginx-rtmp-module](https://github.com/arut/nginx-rtmp-module) 将视频流转发到不同的平台，比如 FB Live、YouTube Live 和 Twitch。
不幸的是`nginx-rtmp-module`不支持对一个`rtmps://`地址的重放，而且这个特性不应该被添加到最近的版本中。有一个问题正在讨论中。

RTMPS，这是通过 TLS/SSL 连接的 RTMP。我们可以使用隧道软件与服务器建立 SSL 连接，并通过连接传递 RTMP 流。

Stunnel 是一个代理，用于在不改变程序代码的情况下，为现有的客户端和服务器添加 TLS 加密功能。
Stunnel 使用 OpenSSL 库进行加密，因此它支持编译到库中的任何加密算法。

我们可以用软件包管理器在现代操作系统上安装`stunnel`。

* * *

我的当前设置没有`stunnel`

```
{OBS/FFmpeg/...} -> rtmp://nginx-rtmp-server:1935 -> rtmp://live-api-s.facebook.com:80 
```

Enter fullscreen mode Exit fullscreen mode

使用`stunnel`，设置如下:

```
{OBS/FFmpeg/...} -> rtmp://nginx-rtmp-server:1935 -> rtmp://stunnel:19350 -> rtmps://live-api-s.facebook.com:443 
```

Enter fullscreen mode Exit fullscreen mode

`stunnel`正在端口`:19350`上运行，因为 nginx 已经在端口`:1935`上监听。

* * *

我正在用`root`用户在`Debian 9 - stretch`上安装`stunnel`。您需要切换`root`用户或使用`sudo`来执行下面列出的每个命令。

```
# Install stunnel
apt install stunnel4 
```

Enter fullscreen mode Exit fullscreen mode

下面是 stunnel 的最小配置。

```
# Stunnel basic config
cat <<EOF > /etc/stunnel/stunnel.conf 
setuid = stunnel4
setgid = stunnel4
pid=/tmp/stunnel.pid
output = /var/log/stunnel4/stunnel.log
include = /etc/stunnel/conf.d
EOF 
```

Enter fullscreen mode Exit fullscreen mode

在`/etc/default/stunnel4`中启用隧道

```
ENABLE=1 
```

Enter fullscreen mode Exit fullscreen mode

现在为脸书添加一个隧道直播地址

```
# RTMP -> RTMPS tunnel
cat <<EOF > /etc/stunnel/conf.d/fb.conf 
[fb-live]
client = yes
accept = 127.0.0.1:19350
connect = live-api-s.facebook.com:443
verifyChain = no
EOF 
```

Enter fullscreen mode Exit fullscreen mode

*请注意，您需要启用`verifyChain = yes`来防止 MITM 攻击

```
systemctl restart stunnel4 && systemctl status stunnel4 
```

Enter fullscreen mode Exit fullscreen mode

* * *

我们可以运行一个`ffmpeg`命令来测试推流，目标为本地 stunnel 端口`:19350`

```
ffmpeg -re -i /path/to/video.flv -c:v libx264 -c:a aac -f flv rtmps://127.0.0.1:19350/rtmp/<facebook-live-stream-key> 
```

Enter fullscreen mode Exit fullscreen mode

现在隧道可以工作了！

让我们在 nginx rtmp 配置中替换本地 stunnel 端口的 URL。

```
# Change this line
push rtmp://live-api-s.facebook.com:80/rtmp/<facebook-live-stream-key>;
# to
push rtmp://127.0.0.1:19350/rtmp/<facebook-live-stream-key>; 
```

Enter fullscreen mode Exit fullscreen mode

全部完成！

* * *

自由职业者、系统工程师、科技作家
你可以在 Twitter[@刘澜涛](https://twitter.com/liulantao)或 GitHub [@Lax](https://github.com/Lax) 上找到我。