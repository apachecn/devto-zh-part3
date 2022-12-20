# 使用 TLS、身份验证和 LetsEncrypt 保护 MongoDB

> 原文：<https://dev.to/bekce/securing-mongodb-with-tls-authentication-and-letsencrypt-30al>

这是一个在公共或私有网络上构建专用 MongoDB 服务器的指南，为您的 PaaS 提供服务，并启用有效的 TLS 证书和身份验证来防范外来者。

我已经检查了一些主要的 MongoDB as a service(又名“云”，如果你喜欢花哨的话)提供商，其中一个名为 mLab 的提供商甚至不支持数据库连接上的 TLS，他们的价格适中的包(不是免费的)对我来说显然是一个笑话。许多其他提供商甚至根本没有在他们的功能列表中提到 TLS/SSL，所以我猜想他们只是假设每个潜在的用例都涉及相同的数据中心专用网络，或者一些 VPN 或 SSH 隧道魔法，等等。或者，也可能是因为他们没有升级到 MongoDB 4.0，而 MongoDB 4.0 极大地改进了 TLS 连接支持。

请注意，在本指南中，我们不会提及任何关于防火墙设置的内容，因为它通常是特定于供应商的。
您应该基本上允许端口 22、80 和 27017，这样设置才能正常工作。
你也可以通过`systemctl disable firewalld`禁用 CentOS 服务器中的默认防火墙，这是你的决定。

1.我通常选择 CentOS 来制作产品，因为 Ubuntu 太不稳定了(也就是说，你让一个服务器运行几年，然后*砰！*，他们改变了 *init* 系统！).用`root`运行一切，不要在这里胡说八道。

2.MongoDB 有时可能需要大量的突发内存，既然你正在读这篇文章，我假设你已经有了一个低内存的预算友好的服务器，所以最好先设置一个交换文件。希望你的服务器有一个固态硬盘！我使用 Vultr.com 已经超过两年了，他们的性价比非常好。

```
# check whether you have swap on
swapon -s
# if not, go on
fallocate -l 4G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
# check again
swapon -s
free -m
# make permanent
echo '/swapfile   swap    swap    sw  0   0' >> /etc/fstab 
```

3.安装 MongoDB 4.0。由于改进的 TLS 安全性和本机库的使用，我们鼓励您只使用 4+版本。任何更新的版本(4.2、4.4 等)都可以，只要您使用最新的 yum 存储库。

```
cat>/etc/yum.repos.d/mongodb-org-4.0.repo <<\EOF [mongodb-org-4.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc EOF yum install -y mongodb-org libcurl openssl
service mongod start
systemctl enable mongod 
```

在`mongo` shell 中运行，添加您的第一个用户，如下所示。

阅读此以了解如何添加更多用户和角色。
建议不要使用项目管理用户，而是为你想开发的每个应用程序/项目设置一个新的用户帐户，并且只给一组特定的数据库权限。
您可以拥有一台 mongodb 服务器来满足多个应用程序/项目的需求，而不会影响安全性(假设 mongod 在实现中没有缺陷)。

```
use admin
db.createUser(
  {
    user: "admin",
    pwd: "password",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ]
  }
) 
```

4.安装故障 2ban。这将保护您的服务器免受某些类型的攻击。

```
yum install epel-release
yum install fail2ban

cat>/etc/fail2ban/jail.local <<\EOF [DEFAULT]
# Ban hosts for one hour:
bantime = 3600

# Override /etc/fail2ban/jail.d/00-firewalld.conf:
banaction = iptables-multiport

[sshd]
enabled = true

[mongo-auth]

enabled = true
filter  = mongo-auth
logpath = /var/log/mongodb/mongod.log
maxretry = 3
port    = 27017
banaction = iptables-multiport[name="mongo", port="27017"]

bantime = 86400
findtime = 300 EOF systemctl enable fail2ban
service fail2ban restart 
```

我还激活了 mongo-auth 插件，因此它将禁止在 mongod 上多次验证失败的客户端。
你可以(也应该)通过尝试从另一台机器登录(在设置好一切直到 end first 之后)并通过`fail2ban-client status mongo-auth`观察 fail2ban 来测试。

5.设置 letsencrypt。首先确保`hostname`确实为您的服务器生成了正确的主机名。该主机名解析为您的服务器的公共 ip。

```
yum -y install yum-utils certbot
certbot certonly --standalone -d my.example.com

cat>/root/renew.sh <<\EOF #!/bin/bash
HOSTNAME=`hostname`
cat /etc/letsencrypt/live/$HOSTNAME/fullchain.pem /etc/letsencrypt/live/$HOSTNAME/privkey.pem > /etc/ssl/mongodb.pem
chmod 644 /etc/ssl/mongodb.pem
service mongod restart EOF chmod +x /root/renew.sh 
```

插入这个你的`crontab -e` :

```
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin

0 0,12 * * * certbot renew --renew-hook /root/renew.sh > /root/certbot-cron.log 2>&1 
```

运行 renew.sh 一次，这将把生成的密钥文件放在正确的位置。

```
/root/renew.sh 
```

此解决方案的唯一“缺陷”是服务器必须每 2 个月重启一次(letsencrypt 证书的更新期)，这将暂时中断连接。唉，大多数客户端实现可以通过在一秒钟内自动重试来优雅地处理它，并且在 oplog 的帮助下，您不太可能丢失任何数据，但是如果您的工作负载不能容忍这一点，请通过设置另一个服务器来使用副本集。

6.调整 mongodb 以接受 ssl 和授权

添加/更改`/etc/mongod.conf`中的以下章节:

```
# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0 # enter 0.0.0.0,:: to bind to all IPv4 and IPv6 addresses or, alternatively, use the net.bindIpAll setting.
  ssl:
    mode: requireSSL
    PEMKeyFile: /etc/ssl/mongodb.pem

security:
  authorization: enabled 
```

重新启动服务:`service mongod restart`

7.试验

现在，SSL 和身份验证已在您的服务器上激活，并准备就绪。
尝试通过`mongo --ssl -u admin -p password --authenticationDatabase "admin" my.example.com`连接到您的服务器。

对于其他应用程序，您需要像这样使用 MongoDB uri:`mongodb://admin:password@my.example.com:27017/db_name?ssl=true`，并单独提供`admin`作为您的 authenticationDatabase 设置。

PS:我从我在一台机器上执行的命令的历史中编译了这个指南，我可能漏掉了中间的一些命令。如果在按顺序运行时有任何问题/错误，请在下面评论，谢谢。

新年快乐 2019！

* * *

帖子也在这里:[https://be kce . github . io/securing-MongoDB-TLS-auth-letsencrypt/](https://bekce.github.io/securing-mongodb-tls-auth-letsencrypt/)