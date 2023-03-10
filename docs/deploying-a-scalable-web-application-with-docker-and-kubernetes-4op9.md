# 使用 Docker 和 Kubernetes 部署可伸缩的 web 应用程序

> 原文：<https://dev.to/loopdelicious/deploying-a-scalable-web-application-with-docker-and-kubernetes-4op9>

**了解如何使用容器和编排引擎测试和部署 Node.js 应用程序。**

随着云应用程序复杂性的增长，有时团队会重组为分布式微服务架构，软件交付周期会变得更快。在整个过程中，DevOps 工程师一直在寻找简化和自动化代码持续部署的方法。

一些团队使用容器，如 [Docker](https://www.docker.com/) ，来简化和自动化他们的应用程序的部署过程。这是一种以可预测和一致的方式打包软件的有效方法。无论您是在笔记本电脑上还是在云中，您都可以预期该软件的行为是相似的。

一旦你在多台机器上运行多个容器，组件的更多实例就不能线性扩展，处理这种不断增长的复杂性会变得有些棘手。许多团队也会通过使用编排引擎来解决这种复杂性，比如 [Kubernetes](https://kubernetes.io/) 。团队使用 Kubernetes 作为更高层次的抽象来管理 Docker 容器技术，并进一步简化管道，以使他们的团队走得更快。

> **我们已经看到了 Kubernetes 的巨大优势——提高工程效率、加快应用交付和简化基础设施。**
> 
> 以前每个学年只能发布 1-2 次代码的团队现在可以每天发布多次代码了！
> 
> **[克里斯·杰克逊，云平台总监&SRE·培生](https://kubernetes.io/case-studies/pearson/)T3】**

你不需要有谷歌或脸书级别的流量来关心你的 web 应用程序的可伸缩性。您可能有不同数量的用户，需要准确预测您的基础设施成本，或者只想更高效地管理您的系统。

为了更好地理解软件容器，我们先来谈谈物理运输容器。

### **为什么使用容器？**

在有人发明实体集装箱之前，码头工人需要专门的技能来处理不同类型的贵重货物。物理集装箱使我们能够标准化货物的运输方式。

例如，使用集装箱装运成捆的柴火有很多原因。

*   **便携性** —您的集装箱可以装载到任何船只上，由任何运输提供商运输，甚至可以转移到卡车上在公路上行驶

<figure>[![](img/2eb0dd776a83884f489e2a32d0a71126.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1N8NEgX4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVZf19QtcEYsMaA912V0pkQ.jpeg) 

<figcaption>照片由[二万赫斯里](https://unsplash.com/photos/RJjY5Hpnifk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/search/photos/shipping-container?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

*   模块化——集装箱都是同样的大小和尺寸，所以在任何港口用来装卸柴火集装箱的起重机也可以用来装卸散装鸡集装箱🐔
*   安全 —你的容器与其他容器隔离，这样运送鱼缸的人就不会溅到鱼水🐟在你的柴火上
*   **可扩展性** —你的柴火只会占据所需的空间，所以你可以占据一个集装箱的一个小角落，也可以在整个船队中预留更多的集装箱

类似于物理运输容器，软件容器对于标准化是有用的。在这种情况下，软件容器标准化了 IT 基础设施。容器是一种将代码及其依赖项打包成构建块的整洁方式，这些构建块可以一致且高效地部署。

*   **可移植性** —容器与主机操作系统相分离，因此它可以在从笔记本电脑到您选择的云的任何设备上运行

<figure>[![](img/694f2848dddd793c7dcbf1e2fdf38ddf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U5ciW1_X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATITMVj7NzicPqhVSeu5WoA.jpeg) 

<figcaption>照片由[马库斯·斯皮斯克](https://unsplash.com/photos/jUWw_NEXjDw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[下](https://unsplash.com/search/photos/software-container?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

*   **模块化** —容器让您能够灵活地创建可轻松互换的应用程序堆栈组件，因此您可以在 web 服务器和数据库等组件之间分离关注点
*   **安全性** —容器是不可变的，因此可以通过替换整个容器来进行更新，从而可以轻松发布安全补丁或快速回滚更新
*   **可扩展性** —容器化的应用程序可以向上扩展以处理额外的负载，也可以向下扩展以在停顿时节省资源

### 容器是如何工作的？

让我们重温一下我们的物理海运集装箱类比，想象一个繁忙的海港，船只整天来来往往。有一个**集装箱清单**，上面列出了被塞进**集装箱**的所有东西的内容和装载顺序。集装箱根据这份货单装箱，然后装船。码头工人将**协调物流，记录集装箱装卸的时间表，管理船只的到达和离开，并与货运公司协调。在这样一个繁忙的港口，我们需要一些非常强大的工具来协调和管理所有这些细节。**

现在，回到软件容器的世界。

Docker 是最流行的开源容器技术之一，它允许您构建、运行、测试和部署分布式应用程序。有很多术语，所以让我们抑制住激动，只解决一些基本问题。

#### 容器图像

该图像将告知如何实例化容器，确定哪些软件组件将运行以及如何运行。您还可以从容器中创建一个映像，并与其他人共享这些规范，这样应用程序在开发人员的笔记本电脑上就可以像在生产环境中一样运行。

#### 容器

这描述了一个虚拟环境，它将应用程序代码与运行应用程序所需的所有二进制文件和库捆绑在一起。因为容器包含了它所有的依赖项，所以您不需要在主机操作系统上安装任何东西，保持它的独立和原始。

#### 容器编排

这指的是协调容器和容器之间的行为，比如调度、资源管理和负载平衡。在复杂或动态的生态系统中，团队将使用编排引擎来控制、管理和自动化这一活动。

<figure>[![](img/ca6206688d69c41f4d263765cb193aba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SMvXZV4l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVum3ohIhfvGjlqi9E1I_LQ.png) 

<figcaption>包含你的兴奋</figcaption>

</figure>

在 Postman 工程团队[重组为微服务架构](https://medium.com/postman-engineering/conquering-the-microservices-dependency-hell-at-postman-with-postman-part-1-introduction-a1ae019bb934)之后，现在每个服务都使用 Docker 来配置自己的环境。每个服务所有者定义他们自己的 Dockerfile，当新代码作为 CI/CD 管道的一部分部署时，从该 docker file 生成映像[。生成的映像被推送到团队的容器注册中心，他们的 Beanstalk 环境被配置为从注册中心提取映像来运行容器。](https://medium.com/postman-engineering/continuous-deployment-with-postman-collections-e2fb0b5d2235)

> 每个服务都可以灵活地配置如何运行它们的服务。因此，服务工程师可以专注于构建应用，而平台工程师可以专注于如何自动构建和部署。
> 
> Docker 接管了配置环境和标准化部署流程的责任。这为我们提供了更快的部署和扩展时间，因为构建在 CI 期间只发生一次。
> 
> [**萨斯瓦特达斯**](https://medium.com/@saswatds) **，邮差处平台工程师**

### **为什么要用 Kubernetes？**

有了微服务架构，许多服务可以进入一个单独的应用程序，并且这些服务中的每一个都可以存在于自己的容器中。甚至一个简单的 web 应用程序也可能不存在于单个容器中。您可能有一个容器用于 web 前端，一个用于后端 API，可能还有一个用于数据存储。如果你开始看到一些流量，你可以旋转你的应用组件的更多实例。

然而，简单地增加更多实例并不能线性扩展。容器允许您扩展，但是大规模管理这些操作可能会很复杂。当你大规模操作时，你将协调容器和容器之间的行为。

这就是像 [Kubernetes](https://kubernetes.io/) 这样的编排引擎发挥作用的时候了。Kubernetes 是一个开源的编排系统，用于自动化部署、扩展和管理容器化的应用程序。作为处理容器管理的更高层次的抽象，需要建立一个有点陡峭的学习曲线，但是它使日常生活变得更容易。

<figure>[![](img/75c75968a696665211addde10f37e4f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k0zM0rPD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATbSeBxXcs2JlfNidGPBA4A.png)

<figcaption>Kubernetes 主节点负责维护集群的期望状态</figcaption>

</figure>

> Kubernetes 简化了您的应用程序的部署过程，并提供了使您的应用程序超级健壮的工具。
> 
> 有了 Kubernetes，您可以实现无停机的滚动部署、服务发现以及轻松更换云提供商的灵活性。
> 
> **[kube sail](https://kubesail.com/)创始人丹·帕斯切克**

### **用 Docker 和 Kubernetes** 部署节点应用的邮差配方

[![](img/483669e4a4434cb7e6d3c95561a30b34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kkWIBzeX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATUNhauq9uCGnkrt-W88MRA.png)

我们来试试吧！我们将从一个简单的节点应用程序开始，它的功能类似于 URL shortener。在我们的例子中，我们将使用猫动词、猫形容词和猫表情符号将一个 URL 转换成不同的 URL🐱—当您在浏览器中输入自定义 URL 时，您将被重定向回原始网站。

使用容器的好处在于，即使我在装有我的操作系统和不同版本的 Node 的机器上开发这个应用程序，你也可以依靠我的容器映像来指定在你的机器上、云中或你选择部署的任何地方无缝运行相同应用程序所需的确切规范。

<figure>[![](img/313f116e22e62c8f5707679e1ac85518.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eCtHzMWJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmbE5--6anAPVjJVSM0-V-A.png) 

<figcaption>使用容器测试和部署 app</figcaption>

</figure>

如果您想继续，那么继续[克隆这个示例](https://github.com/postmanlabs/node-doc-kube)，并按照 README 步骤构建这些 API 的本地版本。

1.  发展
2.  试验
3.  部署

#### 开发 app

让我们从一个节点应用开始，它使用 [React](https://reactjs.org/) 作为前端，使用 [Express](https://expressjs.com/) 作为后端。为了简化这个演示，我们将不实现持久数据存储。

后端归结为 2 个 API。

> 1.  Convert the original URL
>     
>     
> 2.  Redirect the new URL back to the original URL

甚至在您开始开发前端之前，您可以使用 Postman 作为客户端向我们的本地服务器发送请求。每当我们更新我们的有效载荷和检查服务器响应时，它将节省我们的时间。

这是大多数人在想到 Postman 时都知道的典型用例。你可能已经知道了。别担心。我们将学习更多的方法来提高我们的效率。

> 💡**邮差提示**:使用[邮差环境](https://learning.getpostman.com/docs/postman/environments_and_globals/intro_to_environments_and_globals)保存**配置参数**，这样你就可以在服务器设置之间快速切换。例如，我在我的集合中使用了一个名为 url 的环境变量，并为本地开发和生产创建了单独的模板。现在，选择所需的 Postman 环境允许我更快地在 [`http://localhost:5500`](http://localhost:5500) 到 [`https://cat-kube-stateless-prod-2ea747e90b.kubesail.io`](https://cat-kube-stateless-prod-2ea747e90b.kubesail.io) 和其他配置选项之间切换。

在本地服务器上启动这些端点，然后让我们转到 Postman 应用程序。在 Postman app 中找到名为 **catURL** 的 [Postman 模板](https://learning.getpostman.com/docs/postman/launching_postman/newbutton/#templates)，然后导入示例集合和环境。

<figure>[![](img/ce0d8bb4e429af62c2f6d17787bb9f9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cTPLpK45--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AfaFJdgwDY6qXWowFX_WFGQ.png) 

<figcaption>从 Postman [模板](https://learning.getpostman.com/docs/postman/launching_postman/newbutton/#templates)</figcaption>

</figure>

中导入 **catURL** 集合和 **catURL-local** 环境

我们将使用 **catURL** 集合和 **catURL-local** 环境来调试和测试我们的 2 个 API。通读[收藏文档](https://documenter.getpostman.com/view/1559645/S11HvKXW)，亲自试用。

#### 测试 app

在这份食谱中，我们不会涉及如何在 Postman 中编写测试的基础知识，或者如何在 Postman 中运行这些测试的基础知识[。然而，您可以看到在 **catURL** 集合的 tests 选项卡下已经编写了一些测试。请随意添加您自己的测试来验证我们的 API 的行为。](https://medium.com/postman-engineering/acing-your-apis-what-you-need-to-know-for-test-automation-e3fdba3519b9)

请确保更新您的常规 Postman 设置以禁止重定向，以便我们可以在重定向之前检查服务器的响应头。更多关于如何做到这一点的信息请点击。

现在我们已经在 Postman 中手动测试了我们的 API，或者使用[集合运行器](https://learning.getpostman.com/docs/postman/collection_runs/starting_a_collection_run)作为我们走向自动化的第一步，让我们使用 Postman 的开源库[纽曼](https://github.com/postmanlabs/newman)在构建时运行我们的集合。我们有三种选择:

*   **良好实践**:使用位于项目目录中的静态 Postman 集合和环境 [JSON 文件](https://learning.getpostman.com/docs/postman/collections/data_formats/#exporting-and-importing-postman-data)，测试运行在本地服务器上的 API
*   **更好的实践**:仍然在你的本地服务器上测试，使用 [Postman API](https://api.getpostman.com/) 运行这些测试，动态地获取你的集合和环境的最新版本
*   **更好的实践**:仍然使用 Postman API，测试运行在本地服务器托管的容器上的 API，以便您的测试环境完全复制您的生产环境

让我们试试最后一种情况——更好的做法。添加一个部署脚本，在本地容器中构建并启动我们的 API。如果任何 Postman 测试失败，Newman 将返回一个错误代码，在执行任何部署步骤之前立即终止脚本的剩余部分。

<figure>[![](img/120c306831e108e4d73c7a4629f18d3b.png)](https://github.com/postmanlabs/node-doc-kube/blob/master/bin/deploy.sh) 

<figcaption>如果测试没有通过，您的代码就不会部署。</figcaption>

</figure>

> 💡 **POSTMAN 提示**:使用 [Postman API](https://docs.api.getpostman.com/) 获取你的集合或环境的最新版本。使用静态 JSON 文件并不能确保您运行的是最新版本的测试。
> 
> 然后，使用 [Newman](https://github.com/postmanlabs/newman) 运行集合和环境，作为您的持续部署/持续集成(CI/CD)管道的一部分。如果你使用[詹金斯](http://blog.getpostman.com/2015/09/03/how-to-write-powerful-automated-api-tests-with-postman-newman-and-jenkins/)、[特拉维斯 CI](http://blog.getpostman.com/2017/08/23/integrate-api-tests-with-postman-newman-and-travis-ci/) 或任何类似的构建系统，过程是相同的。您将添加一个构建时脚本，告诉 Newman 运行您的 Postman 测试。如果测试没有通过，您的代码就不会部署。

#### 部署 app

在这个例子中，我们将使用 Kubernetes 将我们的前端和后端部署到不同的容器中。因此，我们将在单独的 YAML 文件中描述我们的部署步骤。这些文件将包括您的容器映像、资源分配、所需的副本数量以及其他重要信息。

在这个例子中，我只指定了 API 的一个副本。然而，如果我想有 3 个副本，Kubernetes 会密切关注我的应用程序实例，如果任何一个由于某种原因崩溃，就会替换它们。

<figure>[![](img/1e01bfdc4d6ab9ec55e446d41d52f0a0.png)](https://github.com/postmanlabs/node-doc-kube/blob/master/deployment-prod.yaml) 

<figcaption>后端部署的配置文件</figcaption>

</figure>

对于部署，我使用了一个名为 [Kubesail](https://kubesail.com/) 的托管 Kubernetes 提供程序，它创建了一个免费的托管名称空间。然而，底层部署实用程序 [npx deploy-to-kube](https://github.com/kubesail/deploy-to-kube) 支持任何 Kubernetes 集群。通过在您的应用程序目录中运行它，该实用程序将自动生成 docker 文件，构建和推送部署映像，生成 Kubernetes 配置文件，并在您的 Kubernetes 集群上触发部署。

<figure>[![](img/327ad956b04affb2f648290210e5a737.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WboHKpgk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVdTT9MWoF4EdAFG5QPXfNQ.gif) 

<figcaption>这就有点神奇了。</figcaption>

</figure>

一旦我们的应用程序投入生产，我们可以继续定期测试我们的生产 API，以确保它们仍然正常运行。

*   **良好实践**:在特定的基础上，使用 Postman [集合运行器](https://learning.getpostman.com/docs/postman/collection_runs/starting_a_collection_run/)运行相同的测试，以及使用您的生产配置设置的 Postman 环境。
*   **更好的实践**:设置一个[邮递员监视器](https://learning.getpostman.com/docs/postman/monitors/setting_up_monitor)来调度你的测试，或者使用[纽曼](https://github.com/postmanlabs/newman)设置一个定制的 cron 任务来做同样的事情。

<figure>[![](img/e737851d838d62a106d24f3c56c872ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sZ2YaZD_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Am7w2V5FUqzW2zotQPwXniw.png) 

<figcaption>继续测试您的生产 API 以确保它们正常运行</figcaption>

</figure>

除了基本的监控，Kubernetes 社区还开发了一堆[开源资源](https://github.com/ramitsurana/awesome-kubernetes)用于内省。像[普罗米修斯](https://prometheus.io/)和 [Istio](https://istio.io/) 这样的工具为您的 Kubernetes 集群提供了更高级的日志记录和调试功能。

总之，我们使用本地容器来测试我们的节点应用程序，然后 Kubernetes 在云中的容器上部署前端和后端。在这个过程中，我们使用 Postman 在本地测试我们的 API，然后在发布代码之前，最后一次是在产品中。

[![](img/4d4269be6bbae169e2dbbca6129ec07b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GbbSU4Z7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhzYsNKohfDd8JoXQSXRBaQ.png)

### 关于容器和编排的最终想法

如果你错过了，我想强调在测试中使用容器的重要性。容器映像使复制和运行更接近生产环境的应用程序变得更加容易。

您可以使用您的容器映像在测试环境中对应用程序可靠地运行测试，就像 Postman 工程团队所做的那样。容器还允许您为[并发性能测试](https://medium.com/postman-engineering/dont-get-techcrunched-performance-testing-for-your-http-apis-3196e40f6b70)扩展更多实例。如果你已经发布了一个 bug 到产品中，容器可以让你很容易地将更新回滚到应用程序的前一个版本。

在开发、测试和部署过程中，有很多方法可以使用容器。您选择的策略将由您团队当前的基础设施、人才和目标决定。

* * *