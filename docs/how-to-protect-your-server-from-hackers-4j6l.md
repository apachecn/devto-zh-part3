# 如何保护您的服务器免受黑客攻击

> 原文：<https://dev.to/vkolesov/how-to-protect-your-server-from-hackers-4j6l>

这篇文章最初发表在[我的个人博客](https://vkolesov.com/protected-your-server.html)中。

让你的服务器安全并不是一件难事，但是当大量的例行程序来临时，你可能会忘记这么做。以我的情况来说，ssh 服务器在我买了两周后就被黑了。一天早上，我的邮箱收到了一些来自第三方的辱骂，他们说我的服务器上有“东西”试图入侵他们的服务器。所以，我应该快点解决这个问题。

## 如何发现漏洞

In my case it was simple. I executed next command

```
cat /var/log/auth.log |  grep Accepted
```

and it returns me a list of successful authorization to my server. From the all returned lines I found one IP that is not my own. So, In my case, the SSH was a source of vulnerability.

## 如何保护服务器

简单说说我在购买服务器后需要立即做的事情。

*   更新和升级服务器上的所有软件包；
*   安装 ***ufw*** -普通防火墙；
*   关闭除 SSH、HTTP(s)端口之外的所有服务器端口。
*   安装并配置 ***fail2ban*** 实用程序。它有助于分析/var/log/auth.log 并禁止一些 IP，如果他们做出一些错误的活动；
*   更改 sshd 配置，只接受私钥授权。

## 怎么办？

如果你被黑了，你的服务器被感染了，你需要知道如何研究和清理它。最好的方法是重建 VPS。那是我的案子。我在海兹纳有服务器。从他们的仪表板，可以重新创建(删除和创建新的)具有相同的 IP 在一次点击。所以，我做了。之后，在我的本地 PC 上，用 ssh-keygen 实用程序(是标准 OpenSSH 包的一部分)生成了 SSH 密钥。下面的命令同样适用于 Linux 和 MacOS。

```
ssh-keygen
```

它在~/中创建密钥对。ssh 目录。在那次跑步之后

```
 ssh-copy-id you_user@your_server_id
```

会将您“刚刚创建的”公钥上传到服务器。下一步，登录到服务器并编辑 sshd 的配置文件:

```
nano /etc/ssh/sshd_config
```

在配置中更改 PasswordAuthentication 变量

```
PasswordAuthentication no
```

该指令关闭了使用密码连接的可能性(仅接受使用私钥的连接)

## 安装和调整 ufw 和 fail2ban

我在服务器上使用 ubuntu，所以安装是

```
apt install ufw fail2ban
```

下一步只打开服务器上的 ssh、https 端口，因此:

```
ufw allow ssh
ufw allow 80
ufw allow 443 
```

并启用 ufw:

```
ufw enable
```

下一步是配置 fail2ban 实用程序

```
# make a copy of default config (this copy will overload default params according to manual)
cp  /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
nano /etc/fail2ban/jail.local 
```

在那里找到***" b action = "***并将 ***ufw*** 设置为一个值。在那次重装失败后

```
fail2ban-client reload
```

根据这个简单的配置，来自特定 IP 的任何三次错误尝试访问 ssh 端口都将禁止这个 IP 10 分钟。我个人把禁赛时间改了 7 天。
如何检查状态:

```
fail2ban-client status sshd
```

会在我的箱子里回来

```
 Status for the jail: sshd
|- Filter
|  |- Currently failed: 1
|  |- Total failed: 6
|  `- File list:   /var/log/auth.log
`- Actions
   |- Currently banned: 1
   |- Total banned: 2
   `- Banned IP list:   187.109.168.150 
```

你可以看到，那个 IP 已经被防火墙封锁了。ufw 报告中可能看到的相同内容:

```
ufw status
Status: active

To                         Action      From
--                         ------      ----
Anywhere                   REJECT      187.109.168.150           
80/tcp                     ALLOW       Anywhere                  
22                         ALLOW       Anywhere                  
443                        ALLOW       Anywhere 
```

fail2ban 可以配置为在某些 IP 被禁止时向您的电子邮件发送报告。