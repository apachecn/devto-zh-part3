# 具有 LocalStack 的本地 AWS 环境

> 原文：<https://dev.to/victorcosta/local-aws-environment-with-localstack-32bm>

# 有用的链接

*   [本地堆栈 git 库](https://github.com/localstack/localstack)
*   [Aws 可用服务](https://docs.aws.amazon.com/cli/latest/reference/#available-services)
*   [Aws 文档](https://docs.aws.amazon.com/cli/latest/userguide/aws-cli.pdf)

# [本地堆栈](#localstack)

他们的 git 回购协议中使用的定义非常清楚，所以我们就在这里使用它:

> LocalStack 为开发云应用程序提供了一个易于使用的测试/模拟框架

# 设置

本教程使用 docker-image 来设置 Localstack，因此您应该已经安装了 docker 和 Docker-compose 才能继续学习。

在项目的根目录下创建一个包含以下内容的`docker-compose.localstack.yml`(可以在[这里找到](https://github.com/localstack/localstack#running-in-docker) ):

```
version: '2.1'
services:
...
  localstack:
    image: localstack/localstack
    ports:
      - "4567-4584:4567-4584"
      - "${PORT_WEB_UI-8080}:${PORT_WEB_UI-8080}"
    environment:
      - SERVICES=${SERVICES- }
      - DEBUG=${DEBUG- }
      - DATA_DIR=${DATA_DIR- }
      - PORT_WEB_UI=${PORT_WEB_UI- }
      - LAMBDA_EXECUTOR=${LAMBDA_EXECUTOR- }
      - KINESIS_ERROR_PROBABILITY=${KINESIS_ERROR_PROBABILITY- }
      - DOCKER_HOST=unix:///var/run/docker.sock
    volumes:
      - "${TMPDIR:-/tmp/localstack}:/tmp/localstack" 
```

用`s3`和`dynamodb`旋转本地堆栈 docker 容器

对于 MacOS 用户(以防您的$TMPDIR 包含 Docker 无法挂载的符号链接):

```
TMPDIR=/private$TMPDIR PORT_WEB_UI=9000 SERVICES=s3,dynamodb docker-compose -f docker-compose.localstack.yml up 
```

其他用户:

```
PORT_WEB_UI=9000 SERVICES=s3,dynamodb docker-compose -f docker-compose.localstack.yml up 
```

ℹ️快速引用:
**-f** -指定备用合成文件(默认:docker-compose . yml)
**-d**-作为守护进程运行(后台)

> ⚠️要使用的服务名称必须遵循官方 aws 服务命名惯例，该惯例可在 [aws 可用服务](https://docs.aws.amazon.com/cli/latest/reference/#available-services)中找到

有用的`docker`命令:

*   `docker ps`:列出正在运行的容器
*   `docker logs <container-name>`:输出日志容器

* * *

## Api 调用 LocalStack

访问 http://localhost:9000 以确保您的本地 AWS 正在工作。您应该会看到一个空的仪表板。

让我们对本地 aws 进行一些 api 调用，检查我们是否可以开始与它交互。

> 🔎这里有一份关于 aws-cli 的完整用户指南(转到 S3 部分)

```
# Create a bucket
aws-vault exec scd-stg -- aws s3 mb s3://my-custom-bucket --endpoint-url http://localhost:4572 
```

预期输出或类似:`make_bucket: my-custom-bucket`

```
# List buckets
aws-vault exec scd-stg -- aws s3 ls --endpoint-url http://localhost:4572 
```

预期输出或类似输出:`2006-02-03 14:45:09 my-custom-bucket`

* * *

如果你在下面的评论中有任何问题，请让我知道，或者如果一切都如你所愿，请留下你的反馈。