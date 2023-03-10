# 从 Azure Government 中的现有资源创建 Terraform 脚本

> 原文：<https://dev.to/documentednerd/creating-terraform-scripts-from-existing-resources-in-azure-government-l8l>

最近我在 TerraForm 上做了很多工作，我经常被问到的一个问题是基于现有资源创建 terraform 脚本的能力。

所以用例是这样的:你正在做项目，或者是在 Azure 中拥有大量资源的组织的一部分，你出于各种原因想要开始使用 terraform:

*   能够在你的基础设施中迭代
*   环境管理的一致性
*   变更的代码历史

好消息是有一种工具可以做到这一点。这个工具可以在 github 上的[这里](https://github.com/andyt530/az2tf)找到，还有一个先决条件列表。我在 Azure Commercial 中使用过这个工具，对结果非常满意。我想用这个与 Azure 商业。

***注意=>az2tf 工具上列出了先决条件，但是他们没有列出我需要安装的是 jq，使用“apt-get install jq”。*T3】**

接下来，我们需要配置运行 terraform 的环境。对我来说，我是使用我为 Terraform 配置的环境运行的。在 Git repo 中，有一个 PC 安装文档，指导您如何使用 VS 代码和 Terraform 配置您的环境。然后我可以克隆 git repo，并在我的 Windows 10 机器上使用 Ubuntu 子系统执行 az2tf 工具。

现在，这个工具 az2f 是为了与 azure commercial 一起工作而构建的，为了利用 azure government，必须做一个更改

一旦您创建了环境，并且所有先决条件都存在，您就可以在 vscode 中打开一个“终端”窗口，并连接到 Azure Government。

在。/scripts/resources.sh 和。/scripts/resources2.sh 文件中，您将在第 9 行找到以下内容:

ris= `printf “curl -s -X GET -H \”Authorization: Bearer %s\” -H \”Content-Type: application/json\” [https://management.azure.com/subscriptions/%s/resources?api-version=2017-05-10](https://management.azure.com/subscriptions/%25s/resources?api-version=2017-05-10)” $bt $sub`

请将此行改为:

ris= `printf “curl -s -X GET -H \”Authorization: Bearer %s\” -H \”Content-Type: application/json\” [https://management.usgovcloudapi.net/subscriptions/%s/resources?api-version=2017-05-10](https://management.usgovcloudapi.net/subscriptions/%25s/resources?api-version=2017-05-10)” $bt $sub`

然后，您可以通过在终端中运行以下命令来运行“az2tf”工具:

*。/az2tf . sh-s {订阅 ID }-g {资源组名称}*

这将生成脚本，您将看到在标记为“tf”的结构中创建了一个新文件夹。{订阅 ID}”，其中包含设置环境的所有配置步骤。