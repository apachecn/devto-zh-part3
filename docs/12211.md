# 🐳将 InfluxDB 数据库迁移到 docker-compose

> 原文：<https://dev.to/thibmaek/-migrating-influxdb-databases-to-docker-compose-2kee>

> **这篇博文最初发表在我的[博客](https://thibmaek.com/post/migrating-influxdb-databases-to-docker-compose)T3 上**

# 问题

我有一个全新的英特尔 NUC，我打算用它作为 Docker 的服务器。几周前，我在 Raspberry Pi Zero W 上设置了 InfluxDB 来收集我的家庭助理数据，并用 Telegraf 监控我的机器。

我错误地猜测 Chronograf 中会有一个导入/导出模块，我用它来管理我的 InfluxDB 数据库。不过，这个问题似乎有点复杂。问题本身在于这样一个事实，即 influxd 服务需要在数据库恢复发生之前停止，为了复制这种情况，您必须关闭 Docker 容器...也使得采取行动变得毫无意义。

跟随我找到的最快的解决方案，使用 docker-compose 和一个中间容器

## 备份

在运行 InfluxDB (Rpi Zero)的旧主机上，停止服务并导出数据库。最近推荐的方法是通过可移植的方法:

```
# Ignore this if the service is already running of course.
$ sudo systemctl start influxdb
$ influxd backup -portable -database telegraf ./influxdb-backup/telegraf
$ influxd backup -portable -database home_assistant ./influxdb-backup/home_assistant
$ sudo systemctl stop influxdb 
```

在备份了这两个数据库之后，我用 scp 复制了这些目录，然后将它们复制回 NUC，在那里我将运行 Docker 容器

## 恢复

现在，我已经运行了 InfluxDB 容器来测试与 Chronograf 的连接。因为我认为这种格式可读性更好，所以我使用 docker-compose 来旋转这些容器:

```
version: '2'

services:
  influxdb:
    image: influxdb:latest
    container_name: influxdb
    ports:
      - 8086:8086
    volumes:
      - /opt/appdata/influxdb:/var/lib/influxdb
    restart:
      always 
```

恢复的步骤包括首先关闭这个容器，然后启动一个废弃的容器，将备份目录复制到本地安装的卷/opt/appdata，该卷将保存在 docker-compose 创建的容器中。

首先，用`docker-compose down`(或`docker stop influxdb`)停止我们当前的容器

创建映射数据卷和我们的备份目录的中间容器，然后进入容器的 shell:

```
$ docker run --rm --detach -v /opt/appdata/influxdb:/var/lib/influxdb -v /home/thibmaek/influxdb-backup:/backups -p 8086 influxdb:latest
$ docker exec -it mystifying_kepler /bin/bash 
```

在中间容器中，使用映射的备份文件夹恢复我们的数据库，然后退出:

```
$ influxd restore -portable -database telegraf /backups/telegraf
$ influxd restore -portable -database home_assistant /backups/home_assistant
$ exit 
```

杀死并移除中间容器。重启我们之前的 docker-compose 容器，dbs 应该被恢复:

```
$ docker stop mystifying_kepler
$ docker-compose up -d 
```