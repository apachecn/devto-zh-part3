# Sentry:在 AWS EC2 上运行自托管错误跟踪系统

> 原文：<https://dev.to/setevoy/sentry-running-self-hosted-errors-tracking-system-on-an-aws-ec2-18d7>

[![](img/5cd72231899fa70ccd76448ed1611f4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XPrDB5Dl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2019/05/sentry-logo-1.png) 以前，我们使用基于云的 Sentry 版本，但后来达到了电子邮件限制，我们的后端团队没有收到那些对他们的工作至关重要的通知就离开了。

一个自托管的版本很久以前就计划好了，所以现在我们有一个机会来完善它。

下面的帖子描述了如何在 AWS EC2 实例上启动自托管 Sentry，配置电子邮件并测试是否来自 Python。

这里将使用 [Sentry 内部仓库](https://github.com/getsentry/onpremise)。

运行 AWS EC2，从 Let's Encrypt 配置 SSL，安装 NGINX，Docker 和 Docker Compose。

这些步骤在 AWS EC2 帖子上的[bit warden:a organization ' s password manager self-hosted version installation 中有详细描述，所以我在这里将跳过它们。](https://dev.to/setevoy/bitwarden-an-organization-s-password-manager-self-hosted-version-installation-on-an-aws-ec2-1plg)

使用 EC2 类型 t3.medium 作为 Sentry，至少需要 3 GiB 内存。

### 奔跑的哨兵

克隆存储库:

```
root@bttrm-sentry:/home/admin# mkdir /opt/sentry
root@bttrm-sentry:/home/admin# cd /opt/sentry/
root@bttrm-sentry:/opt/sentry# git clone https://github.com/getsentry/onpremise.git
root@bttrm-sentry:/opt/sentry# cd onpremise/ 
```

为数据和 PostgreSQL 数据库创建 Docker 卷:

```
root@bttrm-sentry:/opt/sentry/onpremise# docker volume create --name=sentry-data && docker volume create --name=sentry-postgres
sentry-data
sentry-postgres 
```

创建配置文件:

```
root@bttrm-sentry:/opt/sentry/onpremise# cp -n .env.example .env 
```

构建图像:

```
root@bttrm-sentry:/opt/sentry/onpremise# docker-compose build
smtp uses an image, skipping
memcached uses an image, skipping
redis uses an image, skipping
postgres uses an image, skipping
Building web
Step 1/1 : FROM sentry:9.1-onbuild
9.1-onbuild: Pulling from library/sentry
...
Successfully built 4840fec904c8
Successfully tagged onpremise_worker:latest 
```

生成密钥:

```
root@bttrm-sentry:/opt/sentry/onpremise# docker-compose run --rm web config generate-secret-key
Creating network "onpremise_default" with the default driver
Pulling smtp (tianon/exim4:)...
latest: Pulling from tianon/exim4
...
Status: Downloaded newer image for tianon/exim4:latest
Pulling memcached (memcached:1.5-alpine)...
1.5-alpine: Pulling from library/memcached
...
Status: Downloaded newer image for memcached:1.5-alpine
Pulling redis (redis:3.2-alpine)...
3.2-alpine: Pulling from library/redis
...
Status: Downloaded newer image for redis:3.2-alpine
Pulling postgres (postgres:9.5)...
9.5: Pulling from library/postgres
...
Creating onpremise_smtp_1      ... done
Creating onpremise_postgres_1  ... done
Creating onpremise_memcached_1 ... done
Creating onpremise_redis_1     ... done
y0%***1yz 
```

输出中的最后一个字符串-с*y0 % * * * 1yz*-是我们的密钥。

编辑`.env`文件并设置这个 SECRET_KEY。

填充数据库并创建管理员用户:

```
root@bttrm-sentry:/opt/sentry/onpremise# docker-compose run --rm web upgrade
Starting onpremise_redis_1    ... done
Starting onpremise_postgres_1  ... done
Starting onpremise_memcached_1 ... done
Starting onpremise_smtp_1      ... done
12:06:24 [WARNING] sentry.utils.geo: settings.GEOIP_PATH_MMDB not configured.
12:06:27 [INFO] sentry.plugins.github: apps-not-configured
Syncing...
Creating tables ...
Creating table django_admin_log
Creating table auth_permission
Creating table auth_group_permissions
Creating table auth_group
Creating table django_content_type
Creating table django_session
Creating table django_site
Creating table south_migrationhistory
Installing custom SQL ...
Installing indexes ...
Installed 0 object(s) from 0 fixture(s)
Migrating...
Running migrations for sentry:
- Migrating forwards to 0472_auto__add_field_sentryapp_author.

...
Created internal Sentry project (slug=internal, id=1)

Would you like to create a user account now? [Y/n]: y
Email: admin@example.com
Password:
Repeat for confirmation:
Should this user be a superuser? [y/N]: Y
User created: admin@example.com
Added to organization: sentry
... 
```

运行堆栈:

```
root@bttrm-sentry:/opt/sentry/onpremise# docker-compose up
onpremise_redis_1 is up-to-date
onpremise_memcached_1 is up-to-date
onpremise_postgres_1 is up-to-date
onpremise_smtp_1 is up-to-date
Creating onpremise_worker_1 ... done
Creating onpremise_web_1    ... done
Creating onpremise_cron_1   ... done
... 
```

检查容器:

```
root@bttrm-sentry:/opt/sentry/onpremise# docker ps 
```

[![](img/9a0432f10709543a1639ada67debb41c.png "Sentry: запуск self-hosted версии системы мониторинга ошибок на AWS EC2")](https://rtfm.co.ua/wp-content/uploads/2019/05/Screenshot_20190518_174813.png)

打开浏览器，检查哨兵是否工作:

[![](img/d2306cc7fda6af3a0b10dbbffb6fd285.png "Sentry: запуск self-hosted версии системы мониторинга ошибок на AWS EC2")](https://rtfm.co.ua/wp-content/uploads/2019/05/Screenshot_20190518_151114.png)

登录，您可以在这里配置电子邮件，但我是稍后使用 [AWS SES](https://rtfm.co.ua/aws-ses-simple-email-service-i-workmail-nastrojka-akkaunta-i-otpravka-pochty/) 完成的:

[![](img/380ba472552275bff4f45335392b3cc3.png "Sentry: запуск self-hosted версии системы мониторинга ошибок на AWS EC2")](https://rtfm.co.ua/wp-content/uploads/2019/05/Screenshot_20190518_151222.png)

按下一步，检查它的工作:

[![](img/0dea17145264427ed2f36369eba6b79f.png "Sentry: запуск self-hosted версии системы мониторинга ошибок на AWS EC2")](https://rtfm.co.ua/wp-content/uploads/2019/05/Screenshot_20190518_151731.png)

不确定这里的工人消息是什么，但后来就消失了。

现在，停止容器并创建`systemd`单元文件-`/etc/systemd/system/sentry.service`:

```
root@bttrm-sentry:/opt/sentry/onpremise# systemctl edit --force sentry 
```

在此设定:

```
[Unit]
Description=Sentry service
Requires=docker.service
After=docker.service

[Service]
Restart=always
WorkingDirectory=/opt/sentry/onpremise
# Compose up
ExecStart=/usr/local/bin/docker-compose -f docker-compose.yml up
# Compose down, remove containers and volumes
ExecStop=/usr/local/bin/docker-compose -f docker-compose.yml down -v

[Install]
WantedBy=multi-user.target 
```

如果出现以下错误:

> 5 月 18 日 12:38:33 bttrm-sentry systemd[1]:sentry . Service:Service 有多个 ExecStart= setting，这仅允许用于 Type=oneshot 服务。拒绝。

再加一个`ExecStart`，就空:

```
[Unit]
Description=Sentry service
Requires=docker.service
After=docker.service

[Service]
Restart=always
WorkingDirectory=/opt/sentry/onpremise
# Compose up
ExecStart=
ExecStart=/usr/local/bin/docker-compose -f docker-compose.yml up
# Compose down, remove containers and volumes

ExecStop=/usr/local/bin/docker-compose -f docker-compose.yml down -v
[Install]
WantedBy=multi-user.target 
```

没早看到这样的错误，就谷歌了一下这个解决方案[这里> > >](https://github.com/moby/moby/issues/14491) 。

运行服务:

```
root@bttrm-sentry:/opt/sentry/onpremise# systemctl start sentry 
```

检查其状态:

```
root@bttrm-sentry:/opt/sentry/onpremise# systemctl status sentry 
```

好的，都很好。

添加到自动启动:

```
root@bttrm-sentry:/opt/sentry/onpremise# systemctl enable sentry 
```

### 邮件

邮箱配置文档可用[她的> > >](https://docs.sentry.io/server/config/#mail) 。

编辑`docker-compose.yml`，设置变量，这里是 AWS SES 示例:

```
...
    SENTRY_EMAIL_HOST: email-smtp.us-east-1.amazonaws.com
    SENTRY_EMAIL_PORT: 587
    SENTRY_EMAIL_PASSWORD: BH3***gpM
    SENTRY_EMAIL_USER: AKI***OAQ
    SENTRY_EMAIL_USE_TLS: "true"
    SENTRY_SERVER_EMAIL: no-reply@example.com
... 
```

注意“真”周围的引号:

> SENTRY_EMAIL_USE_TLS: "true "

保存并重启哨兵服务:

```
root@bttrm-sentry:/opt/sentry/onpremise# systemctl restart sentry 
```

### 巨蟒哨兵

创建新项目:

[![](img/b4be538560f271f019b24aef1d3e71be.png "Sentry: запуск self-hosted версии системы мониторинга ошибок на AWS EC2")](https://rtfm.co.ua/wp-content/uploads/2019/05/Screenshot_20190518_162522.png)

获取令牌:

[![](img/f3ee3b6d97947e036aafd3c1fbc38287.png "Sentry: запуск self-hosted версии системы мониторинга ошибок на AWS EC2")](https://rtfm.co.ua/wp-content/uploads/2019/05/Screenshot_20190518_162554.png)

从工作站检查–安装 [`sentry-sdk`](https://github.com/getsentry/sentry-python) :

```
[setevoy@setevoy-arch-work ~ ] $ sudo pip install sentry-sdk
[setevoy@setevoy-arch-work ~ ] $ python
...
>>> import sentry_sdk
>>> sentry_sdk.init("https://96e***c44@sentry.example.com/2")
<sentry_sdk.hub._InitGuard object at 0x7efd4c487128>
>>> sentry_sdk.capture_message("Hello World")
'736347c534cb4e3eb27fb3e3c0641439' 
```

在岗哨中检查事件

[![](img/998058e3c576a16a823e1e1873619256.png "Sentry: запуск self-hosted версии системы мониторинга ошибок на AWS EC2")](https://rtfm.co.ua/wp-content/uploads/2019/05/Screenshot_20190518_163921.png)

和电子邮件:

[![](img/77702087c9676cb76fead5aa8854c75c.png "Sentry: запуск self-hosted версии системы мониторинга ошибок на AWS EC2")](https://rtfm.co.ua/wp-content/uploads/2019/05/Screenshot_20190518_163948.png)

完成了。

### 类似的帖子

*   <small>03/06/2019</small> [普罗米修斯:black box-exporter probe _ http _ status _ code = = 0 及其调试](https://dev.to/setevoy/prometheus-blackbox-exporter-probehttpstatuscode--0-and-its-debug-41mc) <small>(0)</small>
*   <small>03/26/2019</small> [普罗米修斯:警报管理器的警报接收者和基于严重性级别和标签的路由](https://dev.to/setevoy/prometheus-alertmanagers-alerts-receivers-and-routing-based-on-severity-level-and-tags-3f9c) <small>(0)</small>
*   <small>03/10/2019</small> [普罗米修斯:用 ansi ble-Grafana，Loki，和 promtail](https://dev.to/setevoy/prometheus-rtfm-blog-monitoring-set-up-with-ansible--grafana-loki-and-promtail-58i) <small>(0)</small> 设置的 RTFM 博客监控
*   <small>02/07/2019</small>[Grafana Labs:Loki——分布式系统、标签和过滤器](https://rtfm.co.ua/en/grafana-labs-loki-distributed-system-labels-and-filters/) <small>(0)</small>