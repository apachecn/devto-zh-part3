# Jenkins 和 Postman/Newman 的自动化 API 测试

> 原文：<https://dev.to/zaffja/automated-api-testing-with-jenkins-and-postman-newman-29go>

首先，我想说这纯粹是实验性的，我没有在我的工作场所或任何生产环境中使用这个策略。这就是我，在慵懒的周日下午摆弄 API 测试自动化。

在我的工作场所正在寻找一个 API 测试自动化工具，该工具将定期运行并在出现故障时立即通知我们之后，我有了这个想法来与 Jenkins/Postman 一起玩。

我知道 [Postman](https://www.getpostman.com/) 用它的 [Runner](https://learning.getpostman.com/docs/postman/collection_runs/starting_a_collection_run/) 和 [Newman](https://www.npmjs.com/package/newman) 配对运行多个 API 测试是完美的，我可以在无头状态下执行集合。

我在谷歌上搜索如何自动运行邮递员收集过程时偶然发现了詹金斯。所以现在我有了测试和自动化的方法。下面是我跑步邮差+纽曼+詹金斯的分步设置。

我的文件夹结构

```
automated-test
├── docker-compose.yml
├── Dockerfile
├── postman_collections
├── .. # some other Jenkins related stuff that is not in the scope of this post 
```

我首先将官方的詹金斯码头形象扩展到纽曼。

Dockerfile

```
FROM jenkins/jenkins:lts

USER root

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    # install nodejs
    && curl -sL https://deb.nodesource.com/setup_10.x | bash - \
    && apt-get install -y apt-utils \
    && apt-get install -y nodejs \
    && apt-get install -y build-essential \
    && apt-get install -y inotify-tools \
    # install newman
    && npm install -g newman

USER jenkins 
```

然后使用`docker-compose`进行构建(因为我懒得键入一长串 docker 命令来启动 Jenkin 服务器)

坞站-化合物. yml〔t0〕

```
version: "3"

services:
  app:
    build: .
    volumes:
      - .:/var/jenkins_home
    ports:
      - "8080:8080"
      - "50000:50000" 
```

之后，一个简单的`docker-compose up`就足够了。

所以我让我的 Jenkins 服务器启动并运行，现在我需要导出我的 postman 集合。下面是我做的一个简单的 API 测试。它调用一个端点并断言 HTTP 响应状态是 200，很简单吧？
[![](img/3cd9e32749cac3e9b713e9711fa85ab6.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--NCVPpcpR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k4vz7i9stwqlzm2fy4cc.png)

我将收藏导出到`~/automated-test/postman_collections/collection.json`

现在，我需要在`Jenkins`仪表板中创建一个新项目。
[![](img/eecc876b02122315ec7762758b7ec9da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wle93rCX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lc4d1txlhejxe3h13aw2.png) 
值得注意的是

1.  运行测试的时间间隔(我设置为每 5 分钟运行一次)
    `Build Triggers > Build periodically > */5 * * * *`

2.  邮递员集合的实际测试执行
    `Build > Add build step > Execute shell > newman run /var/jenkins_home/postman_collections/collection.json`

一旦保存，詹金斯将执行你的邮递员收集每 5 分钟。
[![](img/3968c898dcd96e272f7b29233564745e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--pkMKHsHS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/te25b4lrh0msq3bnvj56.png)

搞定了。当然，仅仅使用 Jenkins 来测试 Postman API 集合有点矫枉过正，但是它确实完成了任务。如果你有其他合适的自动化工具，请告诉我，因为我的工作场所仍在寻找合适的 API 测试自动化。