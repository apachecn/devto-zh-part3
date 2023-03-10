# monit:SSH 登录时的电子邮件警报

> 原文：<https://dev.to/setevoy/monit-email-alerting-on-an-ssh-logins-1gbc>

[![](img/0725a98dc3d39cd1cbacb4fde58c1f78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RBMwsImF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2017/08/monit_logo-1.png) 任务是当 SSH 从一个现在被列入白名单的 IP 登录时发送一个电子邮件警报。

这里会用 Monit。

安装它:

```
root@jenkins-dev:/home/admin# apt update && apt -y install monit 
```

配置邮件设置:设置*本地主机*(我们这里有一个本地`exim`)，邮件的格式和邮件的接收者。

编辑`/etc/monit/monitrc`文件:

```
...
set mailserver localhost

set mail-format {
  from:    Monit <monit@$HOST>
  subject: monit alert --  $EVENT $SERVICE
  message: $EVENT Service $SERVICE
                Date:        $DATE
                Action:      $ACTION
                Host:        $HOST
                Description: $DESCRIPTION

           Your faithful employee,
           Monit
}

set alert user@example.com 
```

现在添加规则文件`/etc/monit/conf.d/ssh_alerts.conf` :

```
check file ssh_logins with path /var/log/auth.log
  ignore match "/etc/monit/whitelist_ips.txt"
  if match "Accepted publickey" then alert 
```

查看文档关于`IGNORE` [这里> > >](https://mmonit.com/monit/documentation/monit.html#FILE-CONTENT-TEST) 和关于电子邮件的格式——[这里> > >](https://mmonit.com/monit/documentation/monit.html#Message-format) 。

如果不使用基于密钥的授权，而是使用基于密码的授权，则将“*接受的公钥*”更改为“*接受的密码*”。

现在在`/etc/monit/whitelist_ips.txt`文件中添加*1.1.1.1*IP——测试后将设置一个真实的 IP:

重启`monit`服务:

```
root@jenkins-dev:/home/admin# systemctl restart monit 
```

检查它是如何工作的–登录服务器:

```
12:50:21 [setevoy@setevoy-arch-work ~]  $ sshjenkinsdev
...
admin@jenkins-dev:~$ 
```

检查 Monit 的日志:

```
root@jenkins-dev:/home/admin# tail -f /var/log/monit.log
Stored in '/var/lib/monit/id'
[EET Mar 15 12:32:11] info     : Starting Monit 5.20.0 daemon
[EET Mar 15 12:32:11] info     : 'jenkins-dev' Monit 5.20.0 started
[EET Mar 15 12:39:37] info     : Monit daemon with pid [5074] stopped
[EET Mar 15 12:39:37] info     : 'jenkins-dev' Monit 5.20.0 stopped
[EET Mar 15 12:40:28] info     : Starting Monit 5.20.0 daemon
[EET Mar 15 12:40:28] info     : 'jenkins-dev' Monit 5.20.0 started
[EET Mar 15 12:50:28] error    : 'ssh_logins' content match:
Mar 15 12:50:25 localhost sshd[5262]: Accepted publickey for admin from 194.***.***.26 port 33586 ssh2
[EET Mar 15 12:52:28] info     : 'ssh_logins' content doesn't match 
```

还有一封邮件:

[![](img/c7e176330b9c9225c877dd53cc527db0.png)](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190315_125203.png)

显然，这种方法可以通过改变`if match`条件用于任何事情。

例如，我有一个除我之外的所有人都无法访问的网站，所以我添加了另一个规则:

```
check file nginx_web_access with path /var/log/nginx/example.com-access.log
  ignore match "/etc/monit/whitelist_ips.txt"
  if match "GET" then alert 
```

在这里，任何来自未添加到白名单中的 IP 的请求，我都会收到电子邮件提醒。

下面的`/etc/monit/whitelist_ips.txt`可能是这样的:

```
setevoy@rtfm-do-production:~$ cat /etc/monit/whitelist_ips.txt
194.***.***.26
188.***.***.48 
```

这是我工作网络的一个 IP 地址，另一个是我的家。

完成了。

### 类似的帖子

*   <small>03/06/2019</small> [普罗米修斯:black box-exporter probe _ http _ status _ code = = 0 及其调试](https://dev.to/setevoy/prometheus-blackbox-exporter-probehttpstatuscode--0-and-its-debug-41mc) <small>(0)</small>
*   <small>03/10/2019</small> [普罗米修斯:用 ansi ble-Grafana，Loki，和 promtail](https://dev.to/setevoy/prometheus-rtfm-blog-monitoring-set-up-with-ansible--grafana-loki-and-promtail-58i) <small>(0)</small> 设置的 RTFM 博客监控
*   <small>02/07/2019</small>[Grafana Labs:Loki——分布式系统、标签和过滤器](https://rtfm.co.ua/en/grafana-labs-loki-distributed-system-labels-and-filters/) <small>(0)</small>
*   <small>06/11/2018</small> [普罗米修斯:AWS EC2 服务发现](https://rtfm.co.ua/prometheus-aws-ec2-service-discovery/) <small>(0)</small>