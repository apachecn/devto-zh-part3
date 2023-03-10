# AWS RDS 转储/恢复/查看进度

> 原文：<https://dev.to/piczmar_0/aws-rds-dump--restore--view-progress-bij>

[![](img/a5e4257009f6997978197beaddddae04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uDamIBBx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d28w63qeobp8327jfjkc.png)

我敢打赌，当您为了一些调查、测试甚至开发而不得不转储生产数据库时，您一定遇到过这种情况。

* * *

题外话:
我不会在这里提到敏感数据和数据混淆，因为这超出了本文的范围，但我只想提醒你，你正在处理真实的人的数据，应该记住一些法规，如 GDPR，或者小心不要给真实用户发送电子邮件，如果你正在使用生产数据库测试你的应用程序。
在任何情况下，用一些虚拟值替换真实用户的敏感数据都是好的。

* * *

通常，生产数据库在 AWS VPC(虚拟私有云)中是安全的，没有人能够在没有 VPN 的情况下直接连接。经常发生的情况是，数据库只能从 VPC 内部的一个服务器访问，这个服务器作为一个“堡垒”服务器，所以你可以从这个服务器访问 VPC 内部的其他服务器，但不能从公共服务器访问。

以下是在这些情况下你可能会发现有用的一些提示:

*   将 MySQL 文件从 RDS 数据库直接转储到本地主机上。这意味着您不必将文件转储到“bastion”服务器上，然后将其复制到本地，因为您可以直接在本地创建转储

```
ssh -i ssh_key.pem ec2-user@bastion.host  \
mysqldump -P 3306 -h rds.host -u dbuser --password=dbpassword dbname > dumpfile 
```

这个命令将 ssh 到 bastion 服务器，并在那里执行`mysqldump`命令，但是结果被重定向到您的本地主机文件`dumpfile`。

*   如果你没有足够的勇气从命令行转储 DB，而你更喜欢使用一些图形工具，如 [Sequel Pro](https://www.sequelpro.com) ，你仍然可以使用 [ssh 反向隧道](https://www.howtoforge.com/reverse-ssh-tunneling)来完成。这意味着您将把 RDS 主机的远程端口映射到一个本地主机端口，bastion 服务器将用于传输流量。

```
ssh -i ssh_key.pem -N -L LOCAL_PORT:rds.host:RDS_PORT ec2-user@bastion.host 
```

当它运行时，您应该能够连接到您最喜爱的客户端，例如:

[![Example connection configuration from Sequel Pro](img/e0bb0e0b0081f4e4c465a4c54305108c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ubdgSPGL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eq3knq8rf2659wsrlcvd.png)

*   如果转储或恢复大型数据库，很难看到进度。通常，该命令看起来像挂起，例如，对于恢复:

```
mysql -P DB_PORT -u dbuser --password=dbpassword  dbname < dumpfile 
```

在*nix 系统上有一个很好但不太为人所知的工具叫做 [Pipe Viewer](https://catonmat.net/unix-utilities-pipe-viewer) ，你可以用它来跟踪进度。

-对于转储:

```
mysqldump -P 3306 -h rds.host -u dbuser --password=dbpassword dbname | pv -W > dumpfile 
```

-对于恢复:

```
pv dumpfile | mysql -P DB_PORT -h db.host -u dbuser --password=dbpassword  dbname 
```

然后，您会在 Pipe Viewer 中看到一个漂亮的进度条:

[![Pipe Viewer in action](img/6af202aa5c8be2936823f553e9fcd991.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WzkGW0QP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1n0efvrqzzydq2qx3tfg.png)

* * *

感谢阅读！

你可以在 Twitter 上与我联系，或者订阅我的邮件列表。我会偶尔向你更新我最近的工作。

[![Subscribe](img/020bafc437059bc66cff58fdf28bc246.png)](http://eepurl.com/ghDSdz)