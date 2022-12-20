# 使用 CircleCI、Docker、HashiCorp Terraform 和 Google Cloud 部署应用程序

> 原文：<https://dev.to/circleci/deploy-applications-using-circleci-docker-hashicorp-terraform-and-google-cloud-10l8>

我经常在会议和技术会议上发言，最近我回答了很多关于不断向云平台交付应用程序的问题，例如使用 [HashiCorp Terraform](https://www.terraform.io/) 的[谷歌云](https://cloud.google.com)。在这篇文章中，我将演示如何使用 CI/CD 管道、Docker 和 Terraform 将应用程序部署到 Google Cloud 实例中。在这个例子中，您将使用一个 [Google 容器优化的 OS](https://cloud.google.com/container-optimized-os/docs/) 主机映像创建一个新的 Google Cloud 实例。Google 的容器优化操作系统是计算引擎虚拟机的操作系统映像，针对运行 Docker 容器进行了优化。借助容器优化操作系统，您可以快速、高效、安全地将 Docker 容器放在 Google 云平台上。

本教程还演示了如何使用 Terraform 创建一个新的 Google Cloud 实例，并使用这个 [CI/CD 教程 Docker 映像](https://github.com/datapunkz/python-cicd-workshop/blob/master/tutorial/cicd_101_guide.md)部署应用程序。该图像将从 Docker Hub 中提取，并在从 Terraform 创建的实例上运行。

## 先决条件

开始之前，您需要具备以下条件:

*   一个 [GitHub 账户](https://github.com/join)
*   一个 [CircleCI 账户](https://circleci.com/signup/)
*   a 码头枢纽账户
*   一个[谷歌云账户](https://cloud.google.com/free)

您还需要:

*   在本地分叉或克隆[cicd-101-车间回购](https://github.com/datapunkz/python-cicd-workshop)。
*   完成本教程中的[动手操作 CircleCI 部分，特别是](https://github.com/datapunkz/python-cicd-workshop/blob/rivera-demo/tutorial/cicd_101_guide.md#hands-on-with-circleci)[设置 Docker Hub 凭据环境变量部分](https://github.com/datapunkz/python-cicd-workshop/blob/rivera-demo/tutorial/cicd_101_guide.md#set-project-level-environment-variables)，这是构建将 Docker 映像推送到 Docker Hub 所必需的。
*   确保您的应用程序的 [Docker 映像](https://circleci.com/docs/2.0/circleci-images/)存在于您的 Docker Hub 帐户中。CircleCI 的绿色建筑应该能让你到达那里。

具备所有先决条件后，就可以继续下一部分了。

## 基础设施为代码

基础设施即代码(IaC)是通过机器可读的定义文件来管理和配置云和 IT 资源的过程。IaC 使组织能够使用现代 DevOps 工具(如 [Terraform](https://www.terraform.io/) )调配、管理和销毁计算资源。

在本文中，您将使用 IaC 原则和 Terraform 将您的应用程序部署到 Google Cloud。

## 创建谷歌云平台项目

使用这些指令[创建一个新的谷歌云项目](https://github.com/GoogleCloudPlatform/community/blob/master/tutorials/getting-started-on-gcp-with-terraform/index.md#create-a-google-cloud-platform-project)。

### 创建并获取谷歌云项目凭证

您需要创建[谷歌云凭证](https://github.com/GoogleCloudPlatform/community/blob/master/tutorials/getting-started-on-gcp-with-terraform/index.md#getting-project-credentials)，以便使用 Terraform 执行管理操作。进入[创建服务账户密钥页面](https://console.cloud.google.com/apis/credentials/serviceaccountkey)。选择默认的服务账号或者新建一个，选择 JSON 作为 key 类型，点击**创建**。将这个 JSON 文件保存在`terraform/google_cloud/`的根目录下。

**重要安全提示:**将文件重命名为`cicd_demo_gcp_creds.json`，以保护您的 Google Cloud 凭据不会在公共 GitHub 存储库中发布和暴露。您还可以通过简单地在这个项目的`.gitignore`文件中添加凭证的 JSON 文件名来保护凭证的 JSON 文件不被释放。您必须非常谨慎地对待这个 JSON 文件中的数据，因为一旦暴露，任何拥有这些信息的人都可以侵入您的 Google Cloud 帐户，创建资源，并收取费用。

## 本地安装 Terraform

首先，[在本地安装 Terraform】。](https://www.terraform.io/intro/getting-started/install.html)

## 设置地形

在终端中，运行:

```
cd terraform/google_cloud/
terraform init # this installs the Google Terraform plugins 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您必须更改`main.tf`文件中的一些值。您将更改 Terraform 变量的值以匹配您的信息。将变量`project_name`的`default`标签更改为您之前创建的项目名称:

```
variable "project_name" {
  type = "string"
  default = "cicd-workshops"
} 
```

Enter fullscreen mode Exit fullscreen mode

将变量`docker_declaration`的`default`标签的`image`值:`image: 'ariv3ra/python-cicd-workshop'`更改为您在 CI/CD 教程:
中构建并推送到 Docker Hub 的 Docker 映像

```
variable "docker_declaration" {
  type = "string"
  # Change the image: string to match the Docker image you want to use
  default = "spec:\n  containers:\n    - name: test-docker\n      image: 'ariv3ra/python-cicd-workshop'\n      stdin: false\n      tty: false\n  restartPolicy: Always\n"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 地形图

[`terraform plan`命令](https://www.terraform.io/docs/commands/plan.html)用于创建执行计划。除非明确禁用，否则 Terraform 会执行刷新，然后确定需要执行哪些操作才能达到配置文件中指定的所需状态。这个命令是检查一组更改的执行计划是否符合您的期望的一种方便的方法，而不需要对实际资源或状态进行任何更改。

在终端中，运行:

```
terraform plan -out=plan.txt 
```

Enter fullscreen mode Exit fullscreen mode

这将向您显示一个漂亮的图表，其中列出了 Terraform 将创建或更改的项目。

## 地形应用

[`terraform apply`命令](https://www.terraform.io/docs/commands/apply.html)用于应用所需的更改，以达到所需的配置状态，或由 Terraform 执行计划生成的预定动作集。

在终端中，运行:

```
terraform apply plan.txt 
```

Enter fullscreen mode Exit fullscreen mode

这将执行 Terraform 计划，并尝试根据该计划和定义的 Docker 映像构建一个新的 Google Compute 实例。

## 谷歌计算实例 IP 地址

当 Terraform 完成构建 Google 资产时，您应该看到该实例的公共 IP 地址，它应该类似于下面的输出:

```
Public IP Address = 104.196.11.156 
```

Enter fullscreen mode Exit fullscreen mode

复制列出的 IP 地址或 DNS，并将其粘贴到 web 浏览器中，在末尾附加端口 5000。完整的地址应该是这样的:

```
https://35.237.090.42:5000 
```

Enter fullscreen mode Exit fullscreen mode

新应用程序应该呈现欢迎消息和图像。该应用程序是一个 Docker 容器，由您构建并推送到 CircleCI 的 CI/CD intro tutorial Docker 映像派生而来。

## 地形破坏

现在你已经证明你的 Google Compute 实例和 Docker 容器可以工作了，你应该运行 [`terraform destroy`命令](https://www.terraform.io/docs/commands/destroy.html)来销毁你在本教程中创建的资产。你可以让它继续运行，但要知道，在谷歌云平台上运行任何资产都是有成本的，你可能要为这些成本负责。谷歌为其免费试用注册提供了慷慨的 300 美元信用，但如果你让资产运行，你可以很容易地吃掉它。这取决于你，但是运行`terraform destroy`将关闭任何运行资产。

## 总结

在本文中，您使用 Terraform 从 Docker Hub 将示例应用程序部署到一个活动的 Google Cloud 实例。这个例子演示了在应用程序的 CI/CD 管道在 CircleCI 上构建为绿色后，如何使用 Terraform 手动部署应用程序。通过对项目的 CircleCI `config.yml`文件进行一些额外的调整，您可以配置 CI/CD 管道，以便在管道中使用 Terraform 进行自动部署。配置自动地形部署有点复杂，需要更多的工程知识，但这绝对是可能的。这可能是未来博客帖子的主题，敬请关注！

如果你想了解更多关于 CircleCI 的信息，请查看[文档](https://circleci.com/docs/2.0/)网站。如果你遇到困难，你可以通过[社区](https://discuss.circleci.com/)论坛联系 CircleCI 社区。