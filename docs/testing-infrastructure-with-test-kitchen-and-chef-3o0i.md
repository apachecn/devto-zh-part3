# 使用测试厨房和厨师测试基础设施

> 原文：<https://dev.to/azure/testing-infrastructure-with-test-kitchen-and-chef-3o0i>

*我们已经选出了我们最喜欢的由[迈克尔·克伦普](https://twitter.com/mbcrump)创造的技巧和窍门，以及计划整个四月在 Azure 上发布的新技术内容！错过一天(或更多)？[赶上系列](https://dev.to/t/azureapril/latest)。*

**没有天蓝色？** [抢免费订阅](https://azure.microsoft.com/en-us/free?&WT.mc_id=azureapril_devto-blog-cxa)。

一旦我有了一个可以工作的测试厨房[配置](https://dev.to/azure/building-infrastructure-with-test-kitchen-and-chef-5g99),我就可以从一个项目到另一个项目复制那个配置，根据需要进行修改。我如何为我正在构建的基础设施编写测试呢？

在这篇文章中，我将介绍如何使用 [InSpec](https://www.inspec.io/) 、 [Test Kitchen](https://kitchen.ci/) 和[Kitchen-azurem 驱动程序](https://github.com/test-kitchen/kitchen-azurerm)来测试一个样本 Node.js 项目。这篇文章中的所有内容都依赖于在 Azure 上使用 Test Kitchen 和 Chef 的测试基础设施的工作，所以如果你试图复制这些步骤，请确保首先查看这篇文章。此外，Test Kitchen 可以配置为使用各种供应器和驱动程序，因此如果您使用 Puppet 或 Ansible 例如，你完全可以用这些来代替。

正如昨天的[帖子中提到的，我已经在我的系统上安装了](https://dev.to/azure/building-infrastructure-with-test-kitchen-and-chef-5g99)[厨师开发包(ChefDK)](https://downloads.chef.io/chefdk) ，其中包括 [InSpec](https://www.inspec.io/) 。临近 2018 年底，Chef [发布了](https://blog.chef.io/2018/10/16/announcing-general-availability-of-chef-workstation/)Chef 工作站[作为 Chef 开发套件的替代品。如果您没有 ChefDK，Chef Workstation 是一个很好的起点，其中包含相同的软件。](https://downloads.chef.io/chef-workstation/stable)

在我深入讨论细节之前，本文的目标是展示 InSpec，而不是编写 Chef 代码的完美方式，尤其是对于这个应用程序。例如，有一个 [nodejs 社区指南](https://supermarket.chef.io/cookbooks/nodejs)，它有更多的功能，可以为部署 Node.js/npm 和管理 npm 包提供必要的功能。说完了，我们开始吧！

## 先决条件

*   [Chef 开发套件(ChefDK)](https://downloads.chef.io/chefdk)
*   [服务负责人](https://dev.to/azure/you-gotta-keep-privileges-separated-509)
*   ~/.使用您的服务主体配置配置的 azure/凭据

回到. kitchen.yml 文件的第 14 行，我们可以看到配置*验证器*。