# 用 Docker 加速 InfluxDB 2.0 (Alpha)

> 原文：<https://dev.to/influxdata/spinning-up-influxdb-2-0-alpha-with-docker-14k8>

**警告** : InfluxDB 2.0 (Alpha)应该**而不是**用于任何生产工作负载。

而 InfluxDB 背后的公司 InfluxData 并不提供 alpha 构建的包；我们确实出于各种测试和性能目的运行我们自己的 alpha。为此，我们通过 [Quay.io](https://quay.io) 提供了一个公开的 Docker 图片。

## 入门

### 与 Docker 一起运行

```
docker container run --port 9999:9999 quay.io/influxdb/influxdb:2.0.0-alpha 
```

Enter fullscreen mode Exit fullscreen mode

### 用 Docker 编写运行

```
version: "3"

services:
  influxdb:
    image: quay.io/influxdb/influxdb:2.0.0-alpha
    ports:
      - "9999:9999" 
```

Enter fullscreen mode Exit fullscreen mode

### 坚持不懈

如果您需要在容器重启后保持数据库，您可以随意添加一个卷挂载目录`/root/.influxdbv2`。

## 配置 InfluxDB 2.0 (Alpha)

现在您已经有了一个正在运行的容器，您需要配置一些东西。InfluxDB 2.0 与以前版本的不同之处在于，不能忽略或禁用身份验证。InfluxDB 2.0 也是多租户的，这意味着您需要在设置过程中为您的用户指定一个组织。

### 使用 GUI

您可以通过我们的 GUI 配置一切，使用 [https://localhost:9999](https://localhost:9999) 。

### 使用 CLI

您可以通过我们的 CLI 配置一切，尽管您需要先进入容器。

```
# Docker
docker container exec -it container_name bash

# Docker Compose
docker-compose exec influxdb bash 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经在容器中了，您可以使用我们的`setup`命令来交互地配置一切:

```
influx setup 
```

Enter fullscreen mode Exit fullscreen mode

如果你想通过脚本来实现，你也可以使用命令行标志:

```
Flags:
  -b, --bucket string     primary bucket name
  -f, --force             skip confirmation prompt
  -h, --help              Help for the setup command
  -o, --org string        primary organization name
  -p, --password string   password for username
  -r, --retention int     retention period in hours, else infinite (default -1)
  -u, --username string   primary username 
```

Enter fullscreen mode Exit fullscreen mode

#### API Token

在安装过程中指定一个令牌也将允许您为将来的请求设置 API 令牌。省略这个会为你生成一个并存储在`/root/.influxdbv2/credentials`(容器内)。

您可以这样指定令牌:

```
-t, --token string   API token to be used throughout client calls 
```

Enter fullscreen mode Exit fullscreen mode

* * *

就是这样！现在您已经为数据准备好了一个可用的 InfluxDB 2.0。

玩得开心！