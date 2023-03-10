# 使用 Azure CLI 部署应用程序

> 原文：<https://dev.to/azure/deploying-an-app-with-the-cli-2ol5>

欢迎来到蔚蓝的四月！四月的每一天，我们都会发布一篇关于 Azure 的文章。主题范围包括:

*   部署到 Azure
*   Blob 存储
*   逻辑应用
*   机器学习
*   Azure DevOps
*   ARM 模板
*   Azure 函数
*   VS 代码
*   宇宙数据库
*   物联网

还有更多。我很高兴你在这里！我想对迈克尔·克伦普(Michael Crump)表示特别的感谢，因为他创造了我们选择在《蔚蓝的四月》中强调的所有技巧和诀窍。

第一天，我决定从一些相对简单但重要的事情开始。而且常见！仅使用 CLI 将应用部署到 Azure。这是我开始钻研 Azure 时学到的第一件事，我认为它为你的 Azure 知识奠定了基础。

感谢您的加入！你总能在推特上找到我。

> 有关 Azure CLI 的更多信息，请从这里开始。

* * *

虽然我喜欢使用 Azure Portal 甚至 Visual Studio，但有时从命令行做任何事情都很好。在本教程中，我们将使用 Linux VM 和 BASH 来做所有的事情。

**没有天蓝色？** [抢免费订阅](https://azure.microsoft.com/en-us/free?&WT.mc_id=azureapril_devto-blog-cxa)。

#### 第一步

确保您安装了以下堆栈。

```
mbcrump@crumplinux:~$ git --version
git version 2.7.4
mbcrump@crumplinux:~$ nodejs --version
v4.2.6
mbcrump@crumplinux:~$ npm --version
3.5.2
mbcrump@crumplinux:~$ gulp --version
[20:05:28] CLI version 1.4.0
[20:05:28] Local version 3.9.1
mbcrump@crumplinux:~$ mongod --version
db version v2.6.10
2017-09-20T20:11:43.087+0000 git version: nogitversion
2017-09-20T20:11:43.095+0000 OpenSSL version: OpenSSL 1.0.2g  1 Mar 2016 
```

#### 第二步

创建一个文件夹，比如`webapp`，然后是`$ cd webapp`。

#### 第三步

在`webapp`文件夹中运行以下命令。

```
git clone https://github.com/crpietschmann/jsQuizEngine.git. 
```

这是一个基于 JavaScript 的测验引擎，由 Chris Pietschmann 开发。

#### 第四步

将您的工作目录更改为 jsQuizEngine/src，现在我们需要创建一个可以通过 git 部署 web app 的部署用户。

```
az webapp deployment user set --user-name {your-username} --password {your-password} 
```

```
Name    PublishingUserName
------  --------------------
web     mbcrump 
```

#### 第五步

我们需要一个资源组。我打算把我的放在美国西部。

```
az group create --name StaticResourceGroup --location "West US" 
```

```
Location    Name
----------  -------------------
westus      StaticResourceGroup 
```

#### 第六步

我们还需要一个 Azure 应用服务计划。对于这个例子，我将使用免费的。

```
az appservice plan create --name StaticAppServicePlan --resource-group StaticResourceGroup --sku FREE 
```

```
AppServicePlanName    GeoRegion    Kind    Location      MaximumNumberOfWorkers  Name                  ProvisioningState    ResourceGroup        Status    Subscription
--------------------  -----------  ------  ----------  ------------------------  --------------------  -------------------  -------------------  --------  ------------------------------------
StaticAppServicePlan  West US      app     West US                            1  StaticAppServicePlan  Succeeded            StaticResourceGroup  Ready     d1ecc7ac-c1d8-40dc-97d6-2507597e7404 
```

#### 第七步

我们将创建一个 Azure Web 应用程序，并使用本地 git 部署它。

> 记住！您的 URL 将是独一无二的，与您在示例片段中看到的不同。

```
az webapp create --name MyQuizApplication --resource-group StaticResourceGroup --plan StaticAppServicePlan --deployment-local-git 
```

您应该在输出中看到类似这样的内容:

```
Local git is configured with url of 'https://mbcrump@myquizapplication.scm.azurewebsites.net/MyQuizApplication.git' 
```

复制并粘贴到你选择的编辑器中。

#### 第八步

我们需要添加 azure 作为远程存储库，这样我们就可以轻松地从本地 git 存储库中推送更新。请确保将下面的命令更新为您的特定凭据和唯一 URL。

```
git remote add azure https://mbcrump@myquizapplication.scm.azurewebsites.net/MyQuizApplication.git 
```

#### 第九步

推动变革。

```
git push azure master 
```

#### 第十步

不错！我们现在可以浏览我们的新网站。

一旦您掌握了使用 CLI 的窍门，这些步骤就变得很自然了。

**想深入了解 Azure CLI 吗？** [从这里开始](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&WT.mc_id=azureapril_devto-blog-cxa)。

* * *

我们将在 4 月份每天发布文章，所以请继续关注或提前查看更多提示和技巧[现在](http://azuredev.tips)。