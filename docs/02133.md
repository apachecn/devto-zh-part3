# Debian:无人值守升级-自动升级安装，通过 AWS SES 发送电子邮件通知

> 原文：<https://dev.to/setevoy/debian-unattended-upgrades-automatic-upgrades-installation-with-email-notifications-via-aws-ses-59nk>

[![](img/64b150e578e221f867435d91aca472d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NePp9sgS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2013/04/debian-logo_lenny2-e1427785970734.png)`unattended-upgrades`软件包在 Debian/Ubuntu 系统上执行自动升级安装。

这是一个 Python 脚本(1500 行)，位于`/usr/bin/unattended-upgrade`(而`/usr/bin/unattended-upgrades`是指向`/usr/bin/unattended-upgrade`的符号链接)。

CentsOS/RHEL analog – [`yum-cron`](https://rtfm.co.ua/centos-yum-cron-avtomaticheskoe-obnovlenie-sistemy-i-paketov/) .

安装它:

```
$ sudo apt -y install unattended-upgrades 
```

主配置文件是`/etc/apt/apt.conf.d/50unattended-upgrades`，在这里可以配置升级类型、电子邮件设置等。

与升级相关的计划在`/etc/apt/apt.conf.d/20auto-upgrades`文件中完成，该文件可手动创建或使用`dpkg-reconfigure unattended-upgrades`:

[![](img/7b271ac1a8f1a689b8149eecb8cd839f.png)](https://rtfm.co.ua/wp-content/uploads/2019/05/Screenshot_20190523_131750.png)

### `20auto-upgrades`

`/etc/apt/apt.conf.d/20auto-upgrades`选项:

*   `APT::Periodic::Enable`:启用/禁用升级，1 表示启用，0 表示禁用
*   `APT::Periodic::Update-Package-Lists`:以天为单位–运行`apt update`的频率，0 表示完全禁用
*   `APT::Periodic::Download-Upgradeable-Packages`:以天为单位——多久运行一次`apt-get upgrade --download-only`
*   `APT::Periodic::Unattended-Upgrade`:以天为单位——多久运行一次`apt upgrade`
*   `APT::Periodic::AutocleanInterval`:以天为单位——多久运行一次`apt-get autoclean`
*   `APT::Periodic::Verbose`:邮件详细设置:
    *   0–完全禁用
    *   1–整个升级过程
    *   2–同上+包标准输出
    *   3–同上+追踪

### 50 无人值守-升级

#### `Unattended-Upgrade::Origins-Pattern`

`Unattended-Upgrade::Origins-Pattern`描述用于升级的存储库:

```
...
Unattended-Upgrade::Origins-Pattern {
        "origin=Debian,codename=${distro_codename},label=Debian-Security";
};
... 
```

`${distro_codename}`将被 Debian codename 所取代，此时是*的延伸*。

#### `Unattended-Upgrade::Package-Blacklist`

升级期间要忽略的软件包列表:

```
...
Unattended-Upgrade::Package-Blacklist {
   "openjdk-8-jdk";
};
... 
```

#### `Unattended-Upgrade::Remove-Unused-Dependencies`

用`apt-get autoremove` :
删除未使用的包

```
...
Unattended-Upgrade::Remove-Unused-Dependencies "true";
... 
```

#### `Unattended-Upgrade::Mail`

最有用的选项–升级后发送电子邮件通知。使用`mailutils`包中的`mail`。

```
...
Unattended-Upgrade::Mail "user@example.com";
... 
```

或者:

```
...
Unattended-Upgrade::Mail "root";
... 
```

#### `Unattended-Upgrade::MailOnlyOnError`

仅在升级过程中发现问题时发送此类通知:

```
...
Unattended-Upgrade::MailOnlyOnError "true";
... 
```

#### `Unattended-Upgrade::Automatic-Reboot`

如果发现`/var/run/reboot-required`:
，则自动重启服务器

```
...
Unattended-Upgrade::Automatic-Reboot "true";
... 
```

如果没有设置`Automatic-Reboot-Time`，升级后将立即重启。

#### `Unattended-Upgrade::Automatic-Reboot-Time`

如果`Unattended-Upgrade::Automatic-Reboot`设置为*真*，那么`Automatic-Reboot-Time`可用于设置重启时间:

```
...
Unattended-Upgrade::Automatic-Reboot-Time "02:00";
... 
```

### 无人值守运行-升级

配置好一切之后，您可以执行它并进行测试:

```
root@bitwarden-production:/home/admin# unattended-upgrade -v -d --dry-run
Initial blacklisted packages:
Initial whitelisted packages:
Starting unattended upgrades script
Allowed origins are: ['origin=Debian,codename=stretch,label=Debian-Security']
pkgs that look like they should be upgraded:
Fetched 0 B in 0s (0 B/s)
fetch.run() result: 0
blacklist: []
whitelist: []

No packages found that can be upgraded unattended and no pending auto-removals 
```

如果有可用的升级，安装它们:

```
root@bitwarden-production:/home/admin# unattended-upgrade -v -d 
```

### 邮件配置

您可以使用本地 Exim(参见。 [Exim:不支持向远程域发送邮件](https://rtfm.co.ua/exim-mailing-to-remote-domains-not-supported-2/))，但是 Gmail 阻止了该主机的 IP，因此将在此使用 AWS SES。

通过 AWS SES 发送电子邮件–安装本地 SMTP 客户端，例如`ssmtp` :

```
root@bitwarden-production:/home/admin# apt install mailutils ssmtp 
```

编辑`/etc/ssmtp/ssmtp.conf` :

```
root=admin@example.com
mailhub=email-smtp.us-east-1.amazonaws.com:587
AuthUser=AKI***OAQ
AuthPass=BH3***gpM
UseTLS=YES
UseSTARTTLS=YES
hostname=accounts.example.com 
```

配置从发送到 SSMTP 的*邮件–设置一个邮箱，该邮箱在我们的 AWS SES 中配置，否则将收到“ *554 邮件被拒绝:电子邮件地址未验证*”错误。*

编辑`/etc/ssmtp/revaliases`文件:

```
root: no-repy@example.com 
```

检查邮件发送:

```
root@bitwarden-production:/home/admin# echo "Test" | mail -s "Test" admin@example.com 
```

如果在`/var/log/unattended-upgrades/`目录中可以找到日志，则记录日志。

完成了。

### 类似的帖子

*   <small>01/27/2017</small>[Debian:apt–GPG 错误:以下签名无效:KEYEXPIRED](https://rtfm.co.ua/debian-apt-gpg-error-the-following-signatures-were-invalid-keyexpired/) <small>(0)</small>
*   <small>2017 年 7 月 3 日</small>[【azure:send grid】【send grid】【Azur】【azure】](https://rtfm.co.ua/azure-sendgrid-otpravka-pochty-iz-azure/)<small></small>
<small>*   <small>2013 年 2 月 4 日</small>[【debian∶](https://rtfm.co.ua/debian-nastrojka-i-upravlenie-iptables/)*   t010/07/2018 t1arch Linux:ssmtp—发送本地 T3 邮件</small>

<small></small>