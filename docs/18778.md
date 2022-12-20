# OpenSSH:保持连接活动的配置(抑制超时)

> 原文：<https://dev.to/nabbisen/openssh-configuration-to-keep-connection-alive-to-suppress-timeout-3pa6>

[OpenSSH](https://www.openssh.com/index.html) 是由 [OpenBSD 项目](https://www.openbsd.org/)开发的“使用 SSH 协议远程登录的首要连接工具”。

它有一个非常酷的标志，在这个标志中，帕菲穿得非常漂亮😄

[![openssh-logo](img/fc9efd1da5e5b65a008dc51b16a1673e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d6rOTH5B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.openssh.cimg/openssh.gif)

好吧，如果在维护服务器的时候经常断网，那就不舒服了。这篇文章展示了如何配置来保持连接。

<center>✿ ✿ ✿</center>

由于 OpenBSD 项目提供了丰富的文档，手册 [ssh_config](https://man.openbsd.org/ssh_config) 和 [sshd_config](https://man.openbsd.org/sshd_config) 非常有用。

服务器/客户端活动消息用于保持`ssh`连接活动。
它们在客户机和服务器之间发送，请求响应。
再说，它们是通过加密频道发送的。(很好听！)

实际要做的只是在客户机或服务器上编辑关于它们的配置文件。
当然，编辑两个都可以。

# 情况 1:在客户端配置

像这样编辑配置文件:

```
$  nvim ~/.ssh/config 
```

Enter fullscreen mode Exit fullscreen mode

然后，配置 [`ServerAliveInterval`](https://man.openbsd.org/ssh_config#ServerAliveInterval) :

```
ServerAliveInterval 60 
```

Enter fullscreen mode Exit fullscreen mode

`ServerAliveInterval`表示以**秒**为单位的超时间隔。
默认值为 0，表示客户端没有向服务器发送请求响应的消息。

也可以选择 [`ServerAliveCountMax`](https://man.openbsd.org/ssh_config#ServerAliveCountMax) 的配置:

```
ServerAliveCountMax 5 
```

Enter fullscreen mode Exit fullscreen mode

`ServerAliveCountMax`表示发送消息的最大次数。
默认值为 3。

例如，使用上述设置，服务器活动消息的工作方式如下:

> #1.60 秒后，第一条消息从客户端发送到服务器。
> 
> ——当事人:“你还活着吗？”
> -服务器:“是的，我就是！”
> 
> #2.当 60 * 2 = 120 秒过去后，发送第二条消息。
> #3。当 60 * 3 = 180 秒过去后，发送第三条消息。
> #4。当 60 * 4 = 240 秒过去后，发送第 4 条消息。
> #5。当 60 * 5 = 300 秒过去后，发送第 5 条消息。

毕竟，如果服务器没有响应，`ssh`将在大约 300 秒后断开连接。

# 情况 2:服务器中的配置

服务器端配置用于客户端活动消息，其设置与服务器端几乎相同。最显著的区别是，有效消息是从服务器发送到客户端的，而服务器将接收从客户端返回的任何消息。

编辑 [`sshd_config`](https://man.openbsd.org/sshd_config) 这样:

```
#  nvim /etc/ssh/sshd_config 
```

Enter fullscreen mode Exit fullscreen mode

然后，根据需要配置[`ClientAliveInterval`](https://man.openbsd.org/sshd_config#ClientAliveInterval)/[/`ClientAliveCountMax`](https://man.openbsd.org/sshd_config#ClientAliveCountMax)。

<center>✿ ✿ ✿</center>

为🕊服务愉快