# 保护服务器 SSH 的 10 大技巧

> 原文：<https://dev.to/sunil/top-10-tips-to-secure-ssh-your-server-2jab>

[![Secure SSH Server](img/a02b5ee50bf3fab580c37f6007cacb05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5dYZRg3B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/acmeextension.com/wp-content/uploads/secure-ssh-server.jpg%3Ffit%3D800%252C400)

SSH 协议是一种从一台服务器远程登录到另一台服务器的安全方法。它为强认证提供了几个备选选项，并通过强加密保护了通信的安全性和完整性。它是 telnet 和文件传输协议 FTP 等其他协议的安全替代方案。

**定位 SSH 服务器配置文件-**
我们将要讨论的所有更改都将在 SSH 配置文件中完成。这个文件可以在/etc/ssh/ssh_config 中找到

使用-打开此配置文件

```
sudo nano /etc/ssh/ssh_config
```

## 1.使用不同于 22 的端口

22 是 SSH 协议使用的默认端口。您不应该使用默认端口，因为它很容易被猜到，并且更容易受到暴力攻击。

要更改默认端口，请更改下面一行(这里我们使用端口 221，而不是端口 22)。

```
Port 221
```

## 2.仅使用 SSH 2 协议

早期的协议 SSH 1 包含许多安全漏洞。所以您应该使用 SSH 2 而不是 SSH 1。默认情况下，SSH 2 应该设置为 2。如果没有，您可以使用

```
Protocol 2
```

## 3.禁用直接超级用户登录

允许直接 root 登录是最危险的安全漏洞之一。不应该允许任何服务器通过 SSH 引导 root 登录。

要禁用直接超级用户登录，请将 PermitRootLogin 从 yes 更改为 no

```
PermitRootLogin no
```

如果您确实需要以 root 用户身份登录，您可以先添加一个主用户来登录，然后您可以以 root 用户身份登录

要添加用户

```
useradd peter
passwd peter
```

这是一个例子。我强烈建议使用非常强的密码。

登录到用户 peter 后，您可以使用以下命令切换到 root 用户

```
su -
```

## 4.使用公钥而不是密码

登录 SSH 服务器有两种方式——使用密码或公钥/私钥。您应该使用公钥登录 SSH 服务器安全性。您可以使用此链接来配置 SSH 登录，无需密码。

确保基于密钥的登录正常工作后，您可以使用禁用基于密码的登录

```
PasswordAuthentication no
```

## 5.启用双因素身份验证

您应该在 SSH 服务器上启用双因素身份验证，以使它更加安全。因此，如果有人试图暴力破解您的服务器，可以通过双因素认证阻止。

Google authenticator 是最值得信赖、使用最广泛的认证器之一。您可以使用此链接在您的服务器上启用 Google 身份验证。

[**使用 SSH 在远程服务器上执行 PHP 文件**](http://acmeextension.com/execute-php-file-remote-server-using-ssh?from=devto)

## 6.禁用空密码

您不应该允许使用空密码进行远程登录。如果您允许使用空密码登录，您的服务器更容易受到暴力攻击。

禁用空密码登录设置

```
PermitEmptyPasswords no
```

## 7.对 ssh 用户/密钥使用强密码和密码短语

由于密码不安全，大部分服务器都受到了攻击。他们使用容易猜到的密码，如品牌名称或一些通用密码，如 123456 或 qwerty。弱密码更容易被暴力攻击破解。

您应该使用非常强的密码登录您的 SSH 服务器。

## 8.配置空闲超时间隔

为了避免无人值守的 SSH 会话，您可以设置一个空闲超时间隔。

```
ClientAliveInterval 360
ClientAliveCountMax 0
```

您设置的空闲超时间隔以秒为单位(360 秒= 6 分钟)。一旦时间间隔过去，空闲用户将被自动注销。

使用 SSH 在远程服务器上执行 PHP 文件

## 9.禁用端口转发

黑客可以使用端口转发技术，通过 SSH 会话建立隧道网络连接来登录系统。

要禁用端口转发-

```
AllowTcpForwarding no
X11Forwarding no
```

## 10.限制特定 IP 地址的 SSH 登录

默认情况下，SSH 将接受来自任何外部 IP 地址的连接。如果您想将 SSH 限制为只允许来自特定 IP 地址的连接，您可以添加一个 ListenAddress 行。

例如，如果您只想接受来自 IP 地址 192.168.1.2 的 SSH 连接，您可以添加以下行:

```
ListenAddress 192.168.7.2
```

**重启 SSH 服务器**
对 sshd_config 文件进行任何更改后，不要忘记重启 SSH 服务器。

```
sudo service ssh restart
```

参考- [安全 SSH 服务器](http://acmeextension.com/secur-ssh-server?from=devto)

## 结论

通过进行这些更改，您的服务器将比大多数服务器更安全，攻击者也不容易进入您的服务器。