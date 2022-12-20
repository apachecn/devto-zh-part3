# 使用本地命令行或 Robo3T 连接到 Dokku 上的 MongoDB

> 原文：<https://dev.to/hugo__df/connect-to-mongodb-on-dokku-with-your-local-command-line-or-robo3t-47oc>

Dokku mongo 插件提供了一个`mongo:connect`命令，该命令打开一个到 Dokku 上 MongoDB 实例的隧道连接。这就是如何使用您选择的工具连接到您的数据库，示例将使用 Robo3T。

## 先决条件

### dokku-mongo 已安装

参见[github.com/dokku/dokku-mongo](https://github.com/dokku/dokku-mongo)，安装它:

*   以 root 用户身份 ssh 到运行 Dokku 的服务器
*   运行:`sudo dokku plugin:install https://github.com/dokku/dokku-mongo.git mongo`

### 你有数据库可以玩

使用`ssh -t dokku@YOUR_INSTANCE_IP mongo:create public-database`创建一个

您应该得到以下输出，粗体字符串是 mongo 连接 URI:

```
Waiting for container to be ready
=====> MongoDB container created: public-database
=====> Container Information
    Config dir: /var/lib/dokku/services/mongo/public-database/config
    Data dir: /var/lib/dokku/services/mongo/public-database/data
    Dsn: mongodb://public-database:289b4a4b32ab3182895d8aac7def0ee1@dokku-mongo-public-database:27017/public-database
    Exposed ports: -
    Id: 67163f2006dd9e17f8325620814bae9b881ee6465088b8b1c571fbb6c08ac801
    Internal ip: 172.17.0.11
    Links: -
    Service root: /var/lib/dokku/services/mongo/public-database
    Status: running
    Version: mongo:3.4.9 
```

## 连接到 Dokku 上的远程 MongoDB 实例

首先，让我们检查一下是否可以使用包含的 dokku-mongo `:connect`命令进行连接。运行以下命令:

```
ssh -t dokku@YOUR_INSTANCE_IP mongo:connect public-database 
```

它应该会打开一个 MongoDB shell:

```
MongoDB shell version v3.4.9
connecting to: mongodb://127.0.0.1:27017/public-database
MongoDB server version: 3.4.9
> 
```

### Dokku 围绕 MongoDB 访问的行为

如果我们回头看一下`mongo:info`的输出，我们会注意到下面一行:

```
Exposed ports: - 
```

这意味着这个 MongoDB 实例不会暴露给 Dokku 之外的任何东西，这对安全性来说是好的，但对从本地工具访问它来说是坏的。

### 公开我们的 MongoDB 实例

我们需要公开它，并传递一些我们想要映射到的端口(这有助于可预测性/可再现性)

```
ssh -t dokku@YOUR_INSTANCE_IP mongo:expose public-database 20017 20018 20019 20020 
```

这将产生以下输出:

```
-----> Service public-database exposed on port(s) [container->host]: 27017->20017 27018->20018 27019->20019 28017->20020 
```

这意味着 MongoDB 端口 27017、27018、27019 和 28017 分别暴露在服务器的 20017、20018、20019、20020 端口。

### 与 Robo3T 连接

为了连接 Robo3T，我们将创建一个到 MongoDB 实例的新连接。

*   打开“连接”菜单(文件>连接)
*   在第一行中，单击“创建”
*   填写“连接”选项卡，当然是用你实际的服务器 IP 或域名替换你的 _SERVER_IP。

[![](img/85ce8aafaa27113b9d06f6ec2b0899eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EDeSbxjA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_5F4BC56CEE8442961498E30A88107FB43112E81DEC4BBA7ABD2A9A1388573B9B_1546270597650_image.png)

*   如果您记得来自`dokku mongo:info public-database`、`**mongodb://public-database:289b4a4b32ab3182895d8aac7def0ee1@dokku-mongo-public-database:27017/public-database**`的连接字符串，请填写“认证”选项卡
    *   格式为`mongodb://USERNAME:PASSWORD@INTERNAL_HOSTNAME:INTERNAL_PORT/DATABASE_NAME`
    *   所以我们有
    *   数据库:`public-database`
    *   用户名:`public-database`
    *   密码:`289b4a4b32ab3182895d8aac7def0ee1`(使用您的实际密码)

[![](img/4bff24ee11bb160e8180496a623566e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b4iZQbPI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_5F4BC56CEE8442961498E30A88107FB43112E81DEC4BBA7ABD2A9A1388573B9B_1546270788604_image.png)

*   你现在可以点击“测试”，它应该会告诉你一切正常
*   您可以“保存”并“连接”到这个新数据库

## 测试出连接

创建一个新集合“最新公共空间”

[![](img/679867bf75d84bf5bde33629874cbde5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9r_S5VFG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_5F4BC56CEE8442961498E30A88107FB43112E81DEC4BBA7ABD2A9A1388573B9B_1546271101758_image.png)

[![](img/91e84b81de7eb787a2ad56d14f257baf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--axzHIm_z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_5F4BC56CEE8442961498E30A88107FB43112E81DEC4BBA7ABD2A9A1388573B9B_1546271148845_image.png)

插入一些数据

[![](img/8f503bd07e5ceb6a9e9638acfc26894f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y5UF6bQM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_5F4BC56CEE8442961498E30A88107FB43112E81DEC4BBA7ABD2A9A1388573B9B_1546271216423_image.png)

[![](img/416beae58cfe0dfb3e862dbca7ba75ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PoQYhrzN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_5F4BC56CEE8442961498E30A88107FB43112E81DEC4BBA7ABD2A9A1388573B9B_1546271218792_image.png)

让我们断开连接，回到 dokku-mongo 插件连接，并获取`newest-public-spaces`集合的数据，看看我们是否在与同一个数据库对话。

```
ssh -t dokku@YOUR_SERVER_IP mongo:connect public-database
MongoDB shell version v3.4.9
connecting to: mongodb://127.0.0.1:27017/public-database
MongoDB server version: 3.4.9
> db.getCollection('newest-public-spaces').find({})
{ "_id" : ObjectId("5c2a39e6e632f9bc24ec1a2d"), "name" : "my park", "location" : "next door" } 
```

## 安全顾虑

这些连接不是通过 TLS(传输层安全性)来完成的，TLS 对于非敏感数据来说很好。

我还建议在不经常使用连接时使用`dokku mongo:unexpose YOUR_DATABASE_NAME`。

有关 Dokku 和部署的更多信息，请参见:[部署选项:DigitalOcean vs now.sh 上的 Netlify + Dokku、GitHub Pages、Heroku 和 AWS](https://dev.to/hugo__df/deployment-options-netlify--dokku-on-digitalocean-vs-nowsh-github-pages-heroku-and-aws-4cab) 或 Hugo 代码上的[“部署”](https://codewithhugo.com/tags/deployment)类别

如果你正在寻找一个托管你的 Dokku 实例的好地方，我推荐 [DigitalOcean](https://m.do.co/c/4ee8b2d1adb4) ，他们有一个很棒的[一键安装和部署 Dokku](https://www.digitalocean.com/products/one-click-apps/dokku/) ，如果你使用[这个推荐链接，你将获得 100 美元的免费积分](https://m.do.co/c/4ee8b2d1adb4)。

卡梅隆·柯比