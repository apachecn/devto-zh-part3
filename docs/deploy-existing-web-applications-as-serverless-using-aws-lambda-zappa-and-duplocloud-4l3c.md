# 使用 AWS Lambda、Zappa 和 DuploCloud 将现有的 Web 应用程序部署为无服务器的

> 原文：<https://dev.to/duplocloud/deploy-existing-web-applications-as-serverless-using-aws-lambda-zappa-and-duplocloud-4l3c>

无服务器是云计算的最新范例，软件工程师可以部署他们的应用程序，而不必创建或管理服务器基础设施。在本教程中，我们将介绍如何在大约 30 分钟的时间内，学习无服务器并部署现有的 python web 应用程序，而无需对现有的应用程序进行任何代码更改。

此外，使用 DuploLive，您甚至不需要自己的 aws 帐户，更不用说处理任何 AWS 策略和配置了。获得一个免费的@[https://www.duplocloud.com](https://www.duplocloud.com/)帐户，它将在一个普通的 AWS 帐户中为你创建一个安全和独立的工作空间。这附带 100 美元的免费 AWS 使用。不需要信用卡。

享受无服务器！！

精通无服务器基本概念的读者可以直接跳到“ *AWS Lambda、Zappa 和 DuploCloud* ”一节。以下是本教程的视频版本

# 什么是服务器少

传统上，开发人员编写他们的代码并创建一个部署包，部署在云中的服务器上。代码将使用打包技术(如 Docker、debian packages、Windows MSI 等)打包进行部署，并使用部署工具(如 Kubernetes、Chef 等)部署到服务器。

在无服务器模式中，开发人员通过为云平台(例如 AWS)提供以下功能来部署他们的应用，而不是部署在服务器上:

## **1。代码包和基础设施配置:**

代码包需要使用云提供商支持的语言之一(如 python、java、NodeJs)。基础设施配置包括两个类别

a)应用程序需求，如 CPU、内存、环境变量和执行超时。提供程序将在超时后终止该进程。

b)基础设施策略:许可、网络访问，使得执行环境可以访问系统的其他组件，如数据库、其他服务(无服务器或其他)、互联网等。

## 2。触发器:

代码将基于这些触发器定义来执行。一些常见的触发器是 web 应用程序中的 REST 端点、来自 S3 等数据存储的文件更改通知、kinesis 等事件流。

# 为什么和为什么不服务器少？

[![](img/d1ff3679f1a486513da69e4a481a210c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Td52LGcC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1284/1%2AQKKqSbMW8qP0rdmlLaREKA.jpeg)

# AWS Lambda、Zappa 和 DuploCloud

Lambda 是 AWS 的无服务器平台。它支持 Python、NodeJS、Java 和。网芯。在本教程中，我们将部署一个 web 应用程序，因此我们将使用 AWS API gateway 来托管 REST 端点并将其公开给互联网

## **扎帕**

" ***Zappa*** _ 让在 AWS Lambda + API Gateway 上构建和部署所有 Python WSGI 应用程序变得超级简单。可以把它想象成 Python 应用程序的“无服务器”网络托管。这意味着无限扩展、零停机、零维护，而成本只是您当前部署的一小部分！”——【https://github.com/Miserlou/Zappa】[_。([https://github.com/Miserlou/Zappa](https://github.com/Miserlou/Zappa))。)

Zappa 有两个核心功能:

1.  **代码打包:**它接受一个遗留的 python web 应用程序，并创建一个 Lambda 兼容的代码包。在内部，它创建了一个 lambda 函数来包装原始应用程序。在 Lambda 函数中，它解析触发器并将其路由到应用程序中正确的 web API。例如，它隐藏了 flask 应用程序中定义的所有 REST 端点，而只暴露了它创建的 lambda 函数。然而，当接收到请求时，该函数解析该请求，将其作为内联函数调用路由到适当的 flask API。
2.  **AWS 基础设施管理:**它自动将包上传到 S3，创建 IAM 角色、策略、API 网关配置。然而，it ***需要*** 以管理员级别访问用户提供的 AWS 帐户。此外，它是一个 CLI 工具，因此它没有多开发人员隔离<mark>工作区</mark>的概念。开发运维任务，如将 Lambda 部署与应用程序拓扑的其余部分(如数据库、ELB、DynamoDB 等数据存储)或组织的 AWS 部署的其他部分集成，不在范围之内。

Zappa 的基础设施管理方案适用于业余爱好的开发人员或非常小的团队，但并不真正适用于企业级环境，在这种环境下，不可能给每个开发人员这些权限。此外，还需要大量的 DevOps 工作来整合这些东西。这就是 DuploCloud 的用武之地。

## **DuploCloud**

虽然从更广泛的意义上来说，DuploCloud 是一个 DevOps-as-a-service 平台，但它的目的是让多个开发人员能够在 AWS 上部署 lambda 应用程序，而不需要 AWS 帐户本身的任何权限。DuploCloud 通过在单个 AWS 帐户上创建**隔离和安全的工作空间片**来运行。

在公司环境中，基础设施管理员将在公司的 AWS 帐户中安装 DuploCloud，并实例化工作区，将对工作区的访问权授予开发人员。

另一种访问方式是使用 DuploLive，用户只需用他们的 gmail\google 地址注册一个工作空间。他们将在 DuploCloud 拥有的一个公共 AWS 帐户上获得一个隔离且安全的工作区。你需要一个在 DuploCloud 的账户来学习这个教程，你可以在@[https://www.duplocloud.com](https://www.duplocloud.com/)免费得到这个账户。

# 教程:Lambda 上的 Hello World Django 应用

我们将转换一个运行在服务器上的 HelloWorld Django 应用程序，以便在 AWS Lambda 上部署和运行。本教程建立在 macOS Sierra 之上。完整教程的视频可在@

## 1。注册一个双重账户

访问[https://portal.duplocloud.net](https://portal.duplocloud.net/)并点击注册。提供您的电子邮件(一个谷歌帐户)。您将收到一封带有激活链接的电子邮件。

## 2。安装 Zappa 和 DuploCli

我们需要在一个虚拟环境中工作，这样我们就可以将所有的依赖项打包到 lambda 包

```
> virtualenv duplo 

> source duplo/bin/activate

> pip install zappa

> pip install git+git://github.com/duplodemo/duplocli.git 
```

Enter fullscreen mode Exit fullscreen mode

## 3。克隆一个样本 Django Hello World Repo

```
> git clone https://github.com/duplodemo/demoservice.git 
```

Enter fullscreen mode Exit fullscreen mode

## 4。创建 zappa 设置文件

需要这个文件来告诉 zappa 你的应用程序，这样我们就可以把 Django 应用程序转换成 lambda 包

```
> cd demoservice

> cd mysite

> vi zappa_settings.json 
```

Enter fullscreen mode Exit fullscreen mode

粘贴以下内容并保存文件

```
{
    "dev": {
        "django_settings": "mysite.settings",
        "binary_support": false
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

可以参考[https://github.com/Miserlou/Zappa](https://github.com/Miserlou/Zappa)了解详情，以及 flask 等其他 python 库。

## 5。创建 duplo_compose.json

在我们创建 zappa_settings.json 的 mysite 目录中，创建一个名为 duplo_compose.json 的文件，其内容如下。zappa 创建的处理程序总是“handler.lambda_handler ”,所以不应该改变，至于其他的，你可以创建自定义值

```
{
 "LambdaFunctions": [{
      "Description":"Hello World",
      "Environment":{
              "Variables":{
                    "foo":"bar"
                }
      },
      "FunctionName":"helloworld",
      "Handler":"handler.lambda_handler",
      "MemorySize":128,
      "Runtime":"python2.7",
      "Timeout":7
 }]
} 
```

Enter fullscreen mode Exit fullscreen mode

该文件提供了应用程序的基础结构需求。

## 6。构建 lambda 包

在 mysite 目录下安装虚拟环境中的需求

```
> pip install –r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

调用 zappa 来创建包。

```
> zappa package dev 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个 zip 文件，例如 mysite-dev-1506182688.zip

[![](img/c1d68795d9133cc66d95f3f881c2df89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kcg7xAb_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1344/1%2AO2Z34DFYrwa70af7FcVUyQ.jpeg)

## 7。使用 DuploCloud 在 AWS 中部署 lambda 包

如果您已经在步骤 1 中注册了 DuploCloud 帐户，请登录门户。在控制面板中，单击 API 图标，它将显示 cli 命令，如下例屏幕截图所示

[![](img/1661c85a70b56c9f7ab0cfc9d3ea3f63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SpiTNcjS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1288/1%2AqMjN4TfZEg_Vk8qD69eTcQ.jpeg)

复制 CLI 连接命令，并在我们一直工作的虚拟环境(即 mysite 目录)中运行它。

```
> duplocli connection connect …. 
```

Enter fullscreen mode Exit fullscreen mode

现在运行下面的命令来部署 lambda 函数。注意，该命令在单词 lambda 中有两个 aa，这是因为我们使用 python 编写了 cli，而 lambda 是一个语言关键字。如果你有变通方法，请告诉我们@[venkat@duplocloud.net](http://mailto:venkat@duplocloud.net/)。

```
> duplocli lambdaa create-function -n helloworld -c duplo_compose.json -p <zipfilepath> 
```

Enter fullscreen mode Exit fullscreen mode

这里 helloworld 是 duplo_compose.json 中函数的名称，DuploCli 将查找函数 helloworld 的规范，并使用它在 AWS 的工作区中创建一个部署。是 zappa 在步骤 6 中创建的包。上述命令的输出将类似于

[![](img/034610c6823dcbde6451e9533eafaf00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kaDltiMi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2AECQgdbwlHj486rwbaHPZ4Q.jpeg)

## 8。在 AWS 中测试 lambda 应用程序

在 web 浏览器中，再次登录 duplocloud 并转到 AWS 选项卡。你会看到你的 S3 桶和λ函数。以下是我的工作空间的屏幕截图

[![](img/790ed9aeea3d64a778959397df1e2796.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6QufwUhO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2A8mTxYMA9rQWktks83WgJTA.jpeg)

让我们从这里进入 AWS Lambda 控制台，单击 Lambda 功能表下方的第二个 AWS 控制台按钮。在不拥有或管理 AWS 控制台的情况下访问它似乎很神奇。

下面是我的 lambda 函数的屏幕截图。

[![](img/32fb19765976dc94fffe42475fbf4f00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zv4_hT0q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2AK-SbD7eW34plY_gCGg6vEA.jpeg)

点击测试并运行测试。它应该会成功。我们的一个屏幕截图是

[![](img/deb120af51dd77274a6803ac5448a603.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ykvJZHJL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2AhvX8zo8e9JZgy_7F-WkxOA.jpeg)

## 9。通过 APIGateway 公开服务

创建一个 AWS api 网关，向互联网公开 lambda 函数。在这种情况下，我们将创建一个简单的代理 API。这将把接收到的所有流量路由到 lambda 函数。这分两步完成:A)创建 API B)分阶段部署 API。

```
> duplocli apigateway create-api -n helloworld -l duploservices-ca3o68i-helloworld 
```

Enter fullscreen mode Exit fullscreen mode

上面的参数是所需的名称(-n)和 lambda 函数的名称(-l)。输出将是这样的

[![](img/685ef90658afc14c9221d26cc84dcf64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9Y5tZlJ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2A08qED0kkJ3v_p5-YMmJbNQ.jpeg)

在 Duplo 门户中配置已经创建的 API。有时需要一分钟。Duplo portal 中的 AWS 选项卡将如下所示

[![](img/662231efe5a3dd43c41c42c7f205505d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P94WkZUt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2A_jqlgcexdsTQGYKttR182g.jpeg)

## 10。测试 APIGateway Lambda 集成

在 Duplo 门户中，选择表格中的 API 网关行，然后单击下面的 AWS 控制台按钮，它将带您进入 API 网关屏幕，如下所示

[![](img/2087c542a2e52a9545f60728c8ef480d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YElPrBd---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2A6qnWHHvG9dR45s5Wov3FeA.jpeg)

选择第二个 ANY 资源，然后单击 test，如下图所示

[![](img/26f6430d9a0b28d29f5d89aedcd450b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PZUGnDBc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2AlJwuztxBPrOLH11-8ucJFQ.jpeg)

这将带您到下面的测试屏幕，在方法下拉列表中选择“GET ”,并在路径文本框中键入 getInfo。单击底部的测试。结果应该是成功的文本“CICD 演示！”如下图所示。

[![](img/8f04d3ede662f58d3d9a4695b444f4d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9-ghk8Qb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2AgFRmdNVo1RszPMFRg-0xoQ.jpeg)

现在我们已经测试了应用程序 E2E。让我们来“部署”API。在第二个窗格中，操作下拉菜单选择“部署 API”。您需要创建一个新的阶段，将其命名为“dev”并点击 Deploy。这将使 API 公开，并给你一个如下图所示的 URL。

[![](img/ec80876bd7a4949022cb98d3bd10860e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vXGaCDQj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/954/1%2AtytSA99wV8AWKPhzKCHfDw.jpeg)

现在，您可以获取 URL、append /getInfo 并在浏览器中打开它，您应该会看到“CI/CD Demo！”如下图。

[![](img/13c13449f4c60fb7904e0eb9f602b5dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EOdDIApp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2Aj3GFy4tVLRrnIX7YS1QB7A.jpeg)

# 总结

我们使用 Zappa 将一个 HelloWorld Django 应用程序转换成一个 lambda 函数，不需要任何代码更改。我们进一步通过 DuploCloud 将它部署在 AWS 上，不需要开发者的任何许可。

在本教程中，我们使用了 DuploLive(duplocloud.com ),这是一个公共托管服务，它是 DuploCloud 拥有的 AWS 帐户的抽象。每个开发人员都有自己安全且隔离的工作空间。在企业环境中，DuploCloud enterprise 需要由基础架构管理员(devops)以公司 AWS 帐户安装。开发人员(应用程序团队)将在那个环境中拥有他们的工作空间。

如果您有兴趣了解更多关于 DuploCloud 的信息，请联系我们@[info@duplocloud.net](//mailto:info@duplocloud.net)

# 即将到来

在接下来的两个教程中，我们将

1.  将 Lambda 应用程序与 Docker web apps、S3、迪纳摩和 SQL 等其他服务相集成。我们将描述在 Duplo Workspace 中，用户如何在不处理任何 devops 构造的情况下组合不同形式的服务。
2.  与 github.com 集成的 CI/CD 环境中的 Lambda 部署和 QA

帖子[使用 AWS Lambda、Zappa 和 DuploCloud](https://duplocloud.com/blog/deploy-existing-web-applications-as-serverless-using-aws-lambda-zappa-and-duplocloud/) 将现有的 Web 应用部署为无服务器应用最早出现在 [DuploCloud](https://duplocloud.com) 上。