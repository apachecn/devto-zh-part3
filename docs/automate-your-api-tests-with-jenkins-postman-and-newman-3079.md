# 与 Jenkins、Postman 和 Newman 一起自动化您的 API 测试

> 原文：<https://dev.to/carleskapy/automate-your-api-tests-with-jenkins-postman-and-newman-3079>

## 有点背景

如今，CI 管道是我们开发日常工作中必不可少的，帮助我们缩短交付周期，带来更高质量的软件。

作为后端开发人员，API 已经成为我的大部分应用程序的基石，所以测试它们是必须的。

在我们的 CI 管道中有一个 API 测试阶段会让我们对产品质量充满信心，如果有什么地方出了问题，它会提醒我们。

## 邮递员

Postman 是一个伟大的工具，在我们 API 生命周期的每个阶段帮助开发者。它有很棒的[文档](https://learning.getpostman.com/)和越来越多的使用它的开发者社区，所以很容易找到解释很酷的技巧和诀窍的帖子。

### 收藏

Postman 可以轻松地将我们不同的请求组织成[个集合](https://learning.getpostman.com/docs/postman/collections/intro_to_collections)。就我个人而言，我为我想测试的每个项目创建一个集合。然后，在集合内部，我为项目中的每个实体创建一个文件夹，试图覆盖 API 中的每个端点。

[![Requests organized by collections](img/8d3bd18883a147079c6839c4c5d1ff9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xqdevM0N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1v967smnl4xen35arw2k.png) 

<figure>

<figcaption>请求由实体</figcaption>

</figure>

组织

### 环境

Postman 允许我们创建不同的[环境](https://learning.getpostman.com/docs/postman/environments_and_globals/intro_to_environments_and_globals)来运行我们的请求。举一个真实的例子，我会告诉你我在三个不同的环境中工作:我的本地环境、一个登台环境和一个生产环境，所以我创建了三个不同的环境，其中的变量代表了环境之间不断变化的数据(比如 API 请求的基本路径)。这样，我可以定义一个请求，但是很容易地将它指向我的三个不同的环境。想一想改变或添加一个新环境是多么容易！

[![Using environment variables on request url](img/39c1adefd0237b6f22724ed1b0ec5742.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hCj0b6TO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x0fxp2f4xj7in72uw7ge.png) 

<figure>

<figcaption>提取基础路径作为环境变量以针对不同的环境</figcaption>

</figure>

### 测试请求

Postman 最大的特性之一是允许你编写每次请求运行时运行的测试代码。它们是用 JS 编写的，有一套很酷的断言来检查诸如响应代码、响应体或响应头之类的东西。

[![A request with few tests](img/0f29e6b89a98ddcd50f40c22e014aee9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wQnPu2wg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r3zotdaf4r0x96tjwyu1.png) 

<figure>

<figcaption>一个要求用几个测试</figcaption>

</figure>

*甜！*一旦我们编写了一个测试套件来覆盖我们的 API，我们就使用[集合运行器](https://learning.getpostman.com/docs/postman/collection_runs/starting_a_collection_run/)，一个由 Postman 提供的内置工具来运行集合的每个请求并输出测试结果报告。

绿色的草地多美啊...

额外收获:有时，我们想以某种顺序运行我们的请求，猜猜会发生什么？邮递员允许我们这样做！在[检查建立工作流程问题](https://learning.getpostman.com/docs/postman/collection_runs/building_workflows/)T3】

### 到目前为止我们有什么？

至此，我们已经构建了一个 Postman 集合，其中包含一系列请求，这些请求可以针对我们所有环境中的 API。除此之外，每个请求都有几个测试来检查它是否正常工作。活着是多么美好的时光啊！

## 纽曼

你还记得我之前提到的募捐者吗？它可以通过简单的点击运行我们集合中的每个请求。但是用命令行工具来做同样的事情呢？纽曼做到了。Newman 是 Postman 的命令行集合运行程序，允许您直接从命令行运行和测试 Postman 集合，并将其轻松集成到 CI 服务器中，这使它成为我们的一个很好的工具:**用 Jenkins** 自动化我们的 API 测试。

### 运行收藏

为了用 Newman 运行一个集合，我们首先必须从 Postman 导出它。由于我们的一些请求中有一些变量，我们也必须导出合适的环境。一旦我们有了这两个文件，我们就能够使用:
运行收集

```
$ newman run our.postman_collection.json -e our.postman_environment.json 
```

我们将看到收集运行的报告，类似于其他测试框架。

[![Newman CLI test report](img/e885cc75447ebac8f3eafcf17b002f14.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YbAzears--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bhfe61yojs428khstmqj.PNG) 

<figure>

<figcaption>纽曼 CLI 测试报告</figcaption>

</figure>

### 格式化输出

报告可以用不同的方式格式化。为了实现这一点，Newman 提供了一组 **reporters** 来支持不同的输出格式，如 JSON、XUNIT 或 HTML。除此之外，您还可以创建自己的报告程序，将报告格式化成您想要的任何格式。

## 詹金斯

酷，我们已经用 Postman 定义了我们的 *API 测试套件*,我们能够从命令行运行它，并且用 Newman 获得了一个很好的结果报告。但是现在，我们希望将我们令人敬畏的新技能纳入 CI 渠道。

*免责声明:我们使用 Jenkins 作为自动化服务器来建立我们的 CI/CD 管道，我们在 Docker 容器中运行 CI 管道，该容器安装了所有必要的工具(如 Newman)。*

### 把事情搅合起来

本质上，我们需要三个文件来将我们的 API 测试包含在 CI 管道中:

*   Jenkinsfile ，它定义了我们的管道
*   *Postman 集合*，它定义了我们的一组请求和测试
*   *Postman 环境*，定义目标环境的变量

我们将它们全部包含到项目存储库中，因此我们可以从对它们的版本控制中获益，并跟踪它们的变更。

说够了，让我们编码吧！

我们需要在管道中创建一个新的阶段来运行 API 测试。我们通常将它放在单元和集成测试之后，以确保在测试接口之前一切都如预期的那样运行。

```
pipeline {
    ...
    stage('Test API Rest') {
        steps {
            sh 'newman run tests/Newman/our.postman_collection.json -e tests/Newman/env/test.postman_environment.json -r junit,html --reporter-junit-export var/reports/newman/junit/newman.xml --reporter-html-export var/reports/newman/html/index.html'

            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'var/reports/newman/html', reportFiles: 'index.html', reportName: 'Newman API Test', reportTitles: ''])
        }
    }
    ...
} 
```

正如您所看到的，我们调用 Newman 的 run 命令，提供测试环境文件中的集合作为参数。除此之外，我们还通知想要使用哪些报告器(JUNIT、HTML)以及输出将存储在哪里。

作为奖励，我们使用 [HTML PUblisher Jenkins 插件](https://wiki.jenkins.io/display/JENKINS/HTML+Publisher+Plugin)将 HTML 报告链接到构建。

我希望你和我写这篇文章时一样喜欢这段旅程，并且一如既往地欢迎反馈！