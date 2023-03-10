# 应用引擎上的 Ghost 博客

> 原文：<https://dev.to/thomasvl/ghost-blog-on-app-engine-9nh>

[![Ghost blog on App Engine](img/ca83e053340db8087026421565c37b55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sX-yO8Yw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/vanlatum-dev-images/2019/04/Blog1-1.jpg)

在这篇博客中，我们将探讨如何在谷歌应用引擎上托管一个幽灵博客。

App Engine 是一个非常可扩展和可靠的系统，但是它附带了一些你需要考虑的事情。例如，您不能在 App Engine 中存储文件或图像，因为您的博客在容器中运行。但我们可以通过定制的存储适配器从谷歌云存储中提供这些文件。

因为当前的云平台存储适配器似乎无法与最新版本的 ghost 兼容，所以我写了一个适用于谷歌云平台的存储适配器:[https://github.com/thomas-vl/ghost-gcs](https://github.com/thomas-vl/ghost-gcs)

你也不能使用 sqlite3 数据库，所以你需要一个公共可访问的 mysql 数据库。如果您有一个直接管理的虚拟主机，您可以将“%”添加到访问主机列表中，使其公开可用。

让我们一起来建立我们的幽灵博客吧！

先决条件:

1.  从[在谷歌云平台上创建一个项目](https://console.cloud.google.com/projectcreate)开始。
2.  创建一个[存储桶](https://console.cloud.google.com/storage/create-bucket)来存储您的图像。
3.  创建一个[服务帐户](https://console.cloud.google.com/iam-admin/serviceaccounts/create) a .为您的服务帐户命名，然后单击 Create。b .向您的服务帐户添加存储管理员角色。c .通过单击 Create Key 和 Create 生成一个 json 键
4.  在你的机器上下载并安装 [gcloud](https://cloud.google.com/sdk/)
5.  确保你在自己的主机上有一个 mysql 或者在 GCP 上有一个云 sql 数据库

构建您的应用:

1.  在您的机器上创建一个空目录
2.  使用以下内容创建“app.yaml”

```
runtime: custom
env: flex 
```

1.  创建包含以下内容的“Dockerfile”

```
FROM ghost

COPY config.production.json /var/lib/ghost/config.production.json

#install the google cloud storage adapter
WORKDIR /var/lib/ghost
COPY credentials.json /var/lib/ghost/credentials.json
RUN npm install ghost-gcs --no-save 
WORKDIR /var/lib/ghost/content/adapters/storage/ghost-gcs/
ADD https://raw.githubusercontent.com/thomas-vl/ghost-gcs/master/export.js index.js

WORKDIR /var/lib/ghost 
```

1.  使用以下内容创建“config.production.json”

```
{
    "url": "{appengine url}",
    "server": {
      "port": 8080,
      "host": "0.0.0.0"
    },
    "database": {
      "client": "mysql",
      "connection": {
        "host": "{db ip}",
        "port": "{db port}",
        "user": "{db user}",
        "password": "{db password}",
        "database": "{db name}"
      }
    },
    "mail": {
      "transport": "Direct"
    },
    "logging": {
      "transports": [
        "file",
        "stdout"
      ]
    },
    "process": "systemd",
    "paths": {
      "contentPath": "/var/lib/ghost/content"
    },
    "storage": {
        "active": "ghost-gcs",
        "ghost-gcs": {
            "key": "credentials.json",
            "bucket": "{bucket name}"
        }
    }
} 
```

1.  更改以下参数:
    a. {appengine url} -通常为[https://your-project-id.appspot.com](https://your-project-id.appspot.com)T3】b . { db ip }-您的 mysql 数据库 IP 地址
    c. {db port} -通常为 3306
    d. {db user} -您的 mysql 用户
    e. {db password} -您的 mysql 密码
    f. {db name} -您的 msql 数据库名称
    g. {bucket name} -先决条件中创建的 bucket 的全名

2.  确保将下载的服务帐户凭据文件移动到当前目录，并将其重命名为 credentials.json

3.  在终端或 PowerShell 中，转到创建的目录并运行以下命令

    `gcloud app deploy`

享受在你自己主持的幽灵博客上写博客吧！