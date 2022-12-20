# 建设带有测试厨房和厨师的基础设施

> 原文：<https://dev.to/azure/building-infrastructure-with-test-kitchen-and-chef-5g99>

*我们已经选出了我们最喜欢的由[迈克尔·克伦普](https://twitter.com/mbcrump)创造的技巧和窍门，以及计划整个四月在 Azure 上发布的新技术内容！错过一天(或更多)？[赶上系列](https://dev.to/t/azureapril/latest)。*

**没有天蓝色？** [抢免费订阅](https://azure.microsoft.com/en-us/free?&WT.mc_id=azureapril_devto-blog-cxa)。

当我构建基础设施时，在测试我的代码时遵循与开发相同的过程是很重要的，这样我就可以从一致、可靠的构建中受益。它帮助我让新的工程师加入到一个项目中，并减少了个人合作所需的专业知识。

在这篇文章中，我将介绍如何使用[厨师](https://docs.chef.io/)、[测试厨房](https://kitchen.ci/)和[厨房-azurem 驱动程序](https://github.com/test-kitchen/kitchen-azurerm)建立一个项目。Test Kitchen 可以配置为使用各种供应器和驱动程序，所以如果您使用 Puppet 或 Ansible，您完全可以使用它们。在下一篇文章中，我将介绍如何用 [InSpec](https://www.inspec.io/) 编写基础设施测试。

首先，我需要在我的系统上安装适当的软件。该软件可用于各种操作系统，因此它不仅限于 Mac OS，尽管我将用它来描述我的过程。我安装了[Chef Development Kit(Chef dk)](https://downloads.chef.io/chefdk)，它包含了几个不同的软件，对食谱开发有帮助。Cookbooks 是在 Chef 中封装配置和策略的方法。

有时，与 Chef 开发工具包捆绑在一起的一个软件可能需要最新 ChefDK 中没有的更新。例如，如果我想要最新的测试厨房宝石。我按照史蒂文·穆拉夫斯基的方法按需更新宝石。

ChefDK 包括 Test Kitchen、Chef 和 kitchen-azurerm gem，所以我准备在云中构建基础设施。我完全可以用 VirtualBox 或 docker 进行本地开发。

接下来，我需要遵循[自述文件](https://github.com/test-kitchen/kitchen-azurerm/blob/master/README.md)中记录的 kitchen-azurem 驱动程序的先决条件。

我在这里写了一点关于[服务原则的内容](https://dev.to/azure/you-gotta-keep-privileges-separated-509)，所以我将直接为 Test Kitchen 设置一个服务原则，因为我知道我已经将正确的订阅设置为默认。

```
$ docker run -it microsoft/azure-cli
bash-4.4# az login
bash-4.4# az ad sp create-for-rbac --name sigje_test_kitchen --role Contributor 
```

Enter fullscreen mode Exit fullscreen mode

如果你注意到上面的提示发生了变化，那就是我从本地 Mac OS 终端提示符进入 docker 容器时发生的情况！

`az ad sp create-for-rbac`命令的输出将返回一个 JSON 输出，其中包含一些填充`~/.azure/credentials`的关键信息。

在`~/.azure/credentials`中，我如下设置文件，用我的订阅、appID、密码替换 SUBSCRIPTION_ID，用服务主体创建的输出替换 tenant。

```
[SUBSCRIPTION_ID]
client_id = appID
client_secret = password
tenant_id = tenant 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我使用`chef generate`命令创建我的 cookbook 脚手架。

```
$ chef generate cookbook test_software_cookbook 
```

Enter fullscreen mode Exit fullscreen mode

这让我对烹饪书有了最基本的要求。我编辑了`.kitchen.yml`文件，使其看起来更像这样: