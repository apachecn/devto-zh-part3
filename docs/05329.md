# 一个新的安装城际的单一命令

> 原文：<https://dev.to/firmhouse/updated-installing-intercity-to-a-single-command-3ah>

安装 Intercity 变得非常容易——用于管理 Docker 应用程序、插件和应用程序部署的 web UI。从 2018 年 11 月开始，在阿里耶安·德·轰鸣和 T2·耶鲁安·范·巴尔森的出色倡议和支持下，[大公关](https://github.com/intercity/intercity-next/pull/265)终于合并了！🥳

安装和更新 Intercity 的新方法非常简单，只需运行以下命令并遵循安装程序即可:

```
$ wget https://raw.githubusercontent.com/intercity/intercity-next/master/scripts/bootstrap.sh
$ sudo bash bootstrap.sh 
```

运行`bootstrap.sh`脚本将确保 Docker 和 Docker Compose 安装在您的服务器上。它还会询问您一些设置问题和配置设置文件。最后，它将下载并启动运行城际所需的服务，比如带有 LetsEncrypt、PostgreSQL 和 Redis 的 nginx-proxy。

理论上，运行`bootstrap.sh`也将允许你升级到最新版本的城际，因为我们将在 Docker Hub 上发布城际的新版本到官方支持的镜像:【https://hub.docker.com/r/intercity/intercity_next】T2。每次映像更新时，您都可以运行`bootstrap.sh`让 Docker Compose 将其关闭并重启您的服务。

从安装 Intercity 的旧方式升级相当容易，但是您确实需要对 PostgreSQL 数据库文件进行一些文件迁移。一旦我用我个人已经运行的城际部署对此进行了测试，自动升级路径或文档就会准备好。