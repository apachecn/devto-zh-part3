# Azure 上 fastai 的无服务器部署

> 原文：<https://dev.to/flyingnobita/serverless-deployment-of-fastai-on-azure-2ci8>

如何在不提供任何服务器的情况下部署机器学习模型？

*更新:本指南也在 [fast.ai 课程 v3(第一部分)](https://course.fast.ai/deployment_azure_functions.html)中引用。唯一的区别是 fast.ai 课程版本没有提到和使用建议(pipenv & pyenv ),从而使它不那么固执己见。除此之外，两个导轨是相同的。*

## 目录

1.  [FaaS -功能即服务(又名无服务器)](#faas---function-as-a-service-aka-serverless)
    1.  [支持的语言](#supported-languages)
    2.  [存储和内存限制](#storage-and-memory-limitations)
    3.  [时间限制](#time-limitation)
    4.  [fastai 无法使用 pip 在 Windows WSL Ubuntu 中编译(意外)](#fastai-doesnt-compile-in-windows-wsl-ubuntu-using-pip-unexpected)
    5.  [亚马逊 Lambda 和谷歌云功能的存储限制太小(预期)](#storage-limit-from-amazon-lambda-and-google-cloud-functions-is-too-small-expected)
2.  [微软 Azure 功能](#microsoft-azure-functions)
    1.  [定价](#pricing)
3.  [要求](#requirements)
    1.  [软件](#software)
    2.  [账户](#accounts)
4.  [建议](#recommendations)
5.  [1 -本地设置](#1---local-setup)
    1.  [设置项目目录](#setup-project-directory)
    2.  [创建 Azure Functions 项目](#create-azure-functions-project)
    3.  [创建 Azure 函数](#create-azure-function)
    4.  [安装 fastai &依赖关系](#install-fastai--dependencies)
    5.  [更新功能](#update-function)
    6.  [T2`/<FUNCTION_NAME>/ __init__.py`](#function_name%20__init__%20py)
    7.  [T2`/<FUNCTION_NAME>/function.json`](#function_namefunctionjson)
    8.  [T2`export.pkl`](#exportpkl)
    9.  [测试功能](#test-function)
    10.  [检查测试输出](#check-test-outputs)
6.  [双对接设置](#2---docker-setup)
    1.  [构建 Docker 映像](#build-docker-image)
    2.  [测试 Docker 图像](#test-docker-image)
    3.  [推动坞站图像至坞站中枢](#push-docker-image-to-docker-hub)
7.  [3 - Azure 设置](#3---azure-setup)
    1.  [设置 Azure 资源](#setup-azure-resources)
    2.  [创建资源组](#create-resource-group)
    3.  [创建存储账户](#create-storage-account)
    4.  [创建 Linux 应用服务计划](#create-a-linux-app-service-plan)
    5.  [创建应用&从 Docker Hub 部署 Docker 映像](#create-the-app--deploy-the-docker-image-from-docker-hub)
    6.  [配置功能 app](#configure-the-function-app)
    7.  [运行你的 Azure 功能](#run-your-azure-function)
    8.  [删除资源组](#delete-resource-group)
8.  [结论](#conclusion)
9.  [参考文献:](#references)

在[之前的文章](///deployment/2019/02/18/machine_learning_deployment_options.html)中，我们已经研究了为移动应用程序部署训练有素的机器学习模型的不同方法。这包括在移动设备和不同的基于云的架构(IaaS、VPS、PaaS、ML PaaS)上实现推理过程。

在本文中，我将探索最新的产品*无服务器*架构，看看它有什么特点，主要的服务提供者是谁，并使用其中一个提供者在 fastai/PyTorch 中实现一个简单的图像分类器。

## FaaS——功能即服务(又名无服务器)

这类服务器实现将 PaaS 带到了一个全新的水平。

你编写你的代码，这被称为一个*函数*，它可以访问你在云提供商中设置的*资源*，比如照片的在线存储。然后你设置*事件*来触发函数运行。

无服务器有四个主要优势:

1.  无需调配或管理任何硬件
2.  无需为任何闲置资源时间付费
3.  基础架构可以根据负载自动扩展
4.  服务器的可用性和容错能力是内置的

这是一个吸引人的品质列表。听起来每个人都应该没有服务器。

但是你应该吗？

事实上，你应该意识到在金钱和时间上都有[隐性成本](https://medium.com/@amiram_26122/the-hidden-costs-of-serverless-6ced7844780b)。尽管只有当你的应用程序被大量使用时，这些成本才会成为问题(我的意思是像*一个月几百万次*)。第一世界的问题。

与您的应用相关的是云提供商强加的限制，这些限制可能会使您的部署出现问题。其中一些主要的是:

*   #### Supported language

    *   由于设置和维护运行代码的软件框架的责任落在了云提供商身上，他们必须充分利用自己的资源，只支持最流行的语言和框架。您的选择将受到限制，直到版本号。
*   #### Limitation of storage and memory

    *   您通常会受到代码可以访问的磁盘空间和内存量的限制。这对于 ML 应用来说尤其是个问题，因为:
    *   应用通常有一长串的依赖项及其依赖关系(除了 scikit-learn、PyTorch、TensorFlow 等 ML 框架，他们还有 numpy、pandas 等依赖关系。)
    *   包含预训练权重的模型文件可能很大
*   #### Time limit

    *   在强制终止之前，每个功能都有一定的运行时间(通常为 5-10 分钟)。

无服务器仍然是一种新的云计算方式，公司和开发者都开始接受它。然而，已经有很多服务提供商可供选择。我们可以定义两类无服务器服务提供商:

1.  拥有硬件并提供用于访问的 API
2.  不拥有任何硬件，但提供自己的 API 来访问以前类别的硬件

这里我们列出了第一类的主要供应商:

|  | Python 运行时版本 | 部署包大小 | 记忆 | 超时 |
| --- | --- | --- | --- | --- |
| 自动气象站λ | 2.7, 3.6, 3.7 | 50 MB(已压缩) |  |  |
| 250 MB(未压缩) | 3 GB | 900 秒 |  |  |
| 谷歌云功能 | 3.7.1 (beta) | 来源:100 MB(压缩) |  |  |
| 源+模块:500 MB(未压缩) | 2 GB | 540 秒 |  |  |
| IBM OpenWhisk | 2.7.15, 3.6.8, 3.7.2 | 48 兆字节 | 2 GB | 600 秒 |
| 微软 Azure 函数 | 3.6(预览) | ？ | 1.5 GB | 600 秒(消耗计划) |
| 无限制(应用服务计划) |  |  |  |  |

对于一个简单的图像分类应用程序，该函数在内存限制和超时限制内应该没有任何问题。可能的问题是部署包的大小。基本上，将部署包直接上传到无服务器架构可能会失败，因为它可能会大于限制。

磁盘空间限制的一个解决方法是[剥离 ML 库](https://github.com/alecrubin/pytorch-serverless/),这样就只留下绝对需要的东西。[此外，](https://medium.com/@angelatao0123/serving-pytorch-nlp-models-on-aws-lambda-f735190ec16c)你还可以将库分成子模块，这样每个模块都可以适合自己的云功能。进行推理函数调用将触发一系列云函数，最后一个函数将预测结果返回给您。

虽然这些方法有效，但在我看来，它引入了另一个问题。因为 ML 库的精简没有得到官方支持，所以需要做一些工作来将库升级到最新版本。鉴于当今所有 ML 框架的快速发展，这可能不是一个非常可持续的解决方案。

有一个[有趣的方法](https://course.fast.ai/deployment_aws_lambda.html)(也可能是正确的方法)使用 [AWS Lambda 层](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html)来绕过存储限制。AWS Lambda Layers 允许您在 AWS 中以 ZIP 存档的形式组织和存储依赖库。这些档案可以根据需要从 Lambda 函数调用，从而将 Lambda 函数部署包保持在最小，避免 250 MB(未压缩)的大小限制。图层可以公开和共享。有一个包含 PyTorch v1 的层运行在 Python 3.6 上，前面提到的方法使用了这个层。

从上表中可以看出，微软 Azure Functions 没有规定任何限制。

让我们继续来看看第二类无服务器提供商:

| 供应者 | 评论 |
| --- | --- |
| 扎帕 | AWS Lambda 的纯 python API 包装器 |
| 时代周刊 | AWS Lambda 周围的 API 包装器 |
| 无内胎的 | 以 Kubernetes 为中心的 API 包装器，支持许多无服务器的提供者 |
| 无服务器框架 | 支持大多数主要无服务器提供者的 API 包装器 |

这些提供者提供了一个 API 包装器，试图使无服务器体验更加友好，并在他们认为合适的地方为用户添加其他价值。像 Serverless Framework 和 Kubeless 这样的提供商支持多个无服务器基础设施提供商(我们的第一类提供商)。这使得它们特别有用，因为您可以使用一个 API 来部署到它们支持的任何提供者，并且有助于减轻提供者锁定的问题。

在这些提供者中，[无服务器框架](https://serverless.com/)似乎是最有趣的，因为它的免费 API 包装器支持大多数无服务器基础设施提供者，并且支持多种语言。它有一个很大的社区，已经编写了许多[插件](https://github.com/serverless/plugins)，为核心 API 包装增加了额外的功能。

让我们开始使用无服务器并部署到 Amazon Lambda(不使用层)和 Google 云计算，看看我们可能会遇到什么问题:

### fastai 无法使用 pip 在 Windows WSL Ubuntu 中编译(意外)

所有的无服务器架构都需要使用 pip 和 requirements.txt 来安装依赖项，因此我无法使用 conda 来安装 fastai。这导致了很多编译问题，而这些问题在我使用 conda 时并没有出现。我发现这有点令人惊讶，因为我从来没有遇到过 WSL 上的 Ubuntu 和 Ubuntu 之间的差异。事后看来，这是有意义的，因为我只在 ruby 或 node 开发中使用了 WSL Ubuntu。而我总是在 Windows 下使用 conda 进行 Python 开发，这是我使用具有更多编译复杂性的库的时候，conda 帮助解决了这些复杂性。

### 亚马逊 Lambda 和谷歌云功能的存储限制太小(预期)

在我开始在一台真正的 Ubuntu 机器上部署之后，一旦编译问题消失，我就达到了存储极限。

不幸的是，MNIST 的训练模型文件已经是 80 MB 了。当你添加 fastai、PyTorch 和它们的依赖项时，不可能所有东西都适合 GCF 或 AWS Lambda，即使你[压缩了库](https://github.com/UnitedIncome/serverless-python-requirements#dealing-with-lambdas-size-limitations)并通过[启用`slim package`](https://github.com/UnitedIncome/serverless-python-requirements#slim-package) 删除了不必要的文件。

## 微软 Azure 功能

让我们把目光转向 Azure。这不是第一选择，因为[无服务器文档](https://serverless.com/framework/docs/providers/azure/)缺少 Python 实现示例，并且他们的 [Azure 插件](https://github.com/serverless/serverless-azure-functions)已经有一段时间没有更新了。这与官方 [Azure 文档](https://docs.microsoft.com/en-us/azure/azure-functions/)形成鲜明对比，后者详细描述了对 Python 的良好支持。也许 Azure 一直在快速前进，包装 API 还没有足够的时间跟上。

为了试用 Azure，我们需要放弃无服务器框架(以及包装 API 提供的所有好处),直接使用 Azure。值得一试。

### 定价

微软 Azure Functions 提供两种定价，[消费计划](https://docs.microsoft.com/en-us/azure/azure-functions/functions-scale#consumption-plan)和 [App 服务计划](https://docs.microsoft.com/en-us/azure/app-service/overview-hosting-plans)。主要区别在于，消费计划只允许你在函数运行时付费。如果需要，它会为您扩展架构，但您无法控制如何扩展。消费计划定价见[此处](https://azure.microsoft.com/en-us/pricing/details/functions/)。

通过应用服务计划，您可以选择希望功能运行的计算资源级别。无论您的函数是否运行，只要您的资源被定义，您就需要付费。查看[此处](https://azure.microsoft.com/en-us/pricing/details/app-service/windows/)了解应用服务计划定价。

目前，python 仍处于 Azure Functions 的预览阶段，fastai 只有在你在应用服务计划上提供自己的定制 Docker 映像时才能工作。

## 要求

### 软件

*   *真正的* Linux (Windows WSL Ubuntu 不够用。下面是使用 Ubuntu 18.04)
*   (从 PyPI 编译不支持许多 linux 兼容轮子的 fastai 依赖项，例如瓶颈)
*   [Python 3.6](https://www.python.org/downloads/) (目前唯一被 Azure 函数支持的 Python 运行时)
*   [Azure Functions 核心工具版本 2.x](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local#v2)
*   [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)

### 账目

*   [微软 Azure 账户](https://azure.microsoft.com/en-us/)
*   码头枢纽账户

## 推荐

*   [pipenv](https://pipenv.readthedocs.io/en/latest/) (Azure 函数需要 virtualenv，所以不妨使用 pipenv，pipenv 在下面使用 virtualenv)
*   [pyenv](https://github.com/pyenv/pyenv) (万一你用的不是 3.6 的 Python 版本。此外，pyenv 是由 pipenv 在[本地支持的](https://pipenv.readthedocs.io/en/latest/advanced/#automatic-python-installation)

## 1 -本地设置

### 设置项目目录

用你自己的项目目录名替换`<PROJECT_DIR>`。

```
mkdir <PROJECT_DIR>
cd <PROJECT_DIR>
pipenv --python 3.6
pipenv shell 
```

### 创建 Azure 功能项目

创建一个使用 Python 运行时的 Azure Function 项目。这将在`<PROJECT_DIR>`中生成几个文件。

```
func init --docker 
```

出现提示时，选择 python:

*   `Select a worker runtime:` **python**

### 创建 Azure 函数

使用模板`HttpTrigger`创建一个名为`<FUNCTION_NAME>`的函数。用你自己的函数名替换`<FUNCTION_NAME>`。

```
func new --name <FUNCTION_NAME> --template "HttpTrigger" 
```

### 安装 fastai &依赖项

将 Azure 的依赖项添加到 Pipfile。

```
pipenv install -r requirements.txt 
```

在虚拟环境中安装 fastai 和您的应用程序需要的任何其他依赖项。

```
pipenv install fastai 
```

然后将所有的依赖项输出到 requirements.txt，这将在构建 Docker 映像时使用。

```
pipenv lock -r > requirements.txt 
```

### 更新功能

修改目录中的以下文件:

#### `/<FUNCTION_NAME>/ __init__.py`

这就是你的推理函数所在的地方。以下是使用经过训练的图像分类模型的示例。

```
import logging
import os

import azure.functions as func
from fastai.vision import *
import requests

def main(req: func.HttpRequest) -> func.HttpResponse:

    path = Path.cwd()
    learn = load_learner(path)

    request_json = req.get_json()
    r = requests.get(request_json['url'])

    if r.status_code == 200:
        temp_image_name = "temp.jpg"        
        with open(temp_image_name, 'wb') as f:
            f.write(r.content)
    else:
        return func.HttpResponse(f"Image download failed, url: {request_json['url']}")

    img = open_image(temp_image_name)
    pred_class, pred_idx, outputs = learn.predict(img)

    return func.HttpResponse(f"request_json['url']: {request_json['url']}, pred_class: {pred_class}") 
```

#### `/<FUNCTION_NAME>/function.json`

更新函数授权，以便无需任何额外的安全密钥即可调用它。用以下内容替换文件中的相应行:

```
...
      "authLevel": "anonymous",
... 
```

#### `export.pkl`

将你训练好的模型文件`export.pkl`复制到`<PROJECT_DIR>`。

### 测试功能

运行以下命令在本地机器上启动该功能:

```
func host start 
```

这将给你一个带有测试 URL 的输出:

```
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    inference_function: [GET,POST] http://localhost:7071/api/<FUNCTION_NAME> 
```

### 检查测试输出

要检查您的函数是否正常运行，请访问 [http://localhost:7071](http://localhost:7071) ，您应该会看到以下内容:

[![Azure Docker Running Successful Screenshot](img/1058e5f26f91e1af56195946c29540c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t1dJyLd7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/flyingnobita/image/fetch/c_limit%2Cf_auto%2Cq_auto%2Cw_768/https://flyingnobita.com/asseimg/posts/serverless_deployment__azure_docker_test_success_screeshot.webp)

您可以向`http://localhost:7071/api/<FUNCTION_NAME>`发送一个 HTTP POST 方法来检查您的推理函数是否在工作。将`<URL_TO_IMAGE>`替换为一个指向图像的 URL 以进行推理。

```
POST http://localhost:7071/api/<FUNCTION_NAME> HTTP/1.1
content-type: application/json

{
    "url": "<URL_TO_IMAGE>"
} 
```

然后，您应该会看到一个 HTTP 响应:

```
HTTP/1.1 200 OK
Connection: close
Date: Sun, 17 Mar 2019 06:30:29 GMT
Content-Type: text/plain; charset=utf-8
Server: Kestrel
Content-Length: 216

request_json['url']: <URL_TO_IMAGE>, pred_class: <PREDICTED_CLASS> 
```

你应该在`<PREDICTED_CLASS>`中看到你的推理函数预测的类。

准备好之后，您可以按`Ctrl+C`停止测试。

## 双坞站设置

### 构建 Docker 图像

现在您可以构建 Docker 映像，它将包含您的应用程序和它需要运行的所有 python 库。

```
docker build --tag <DOCKER_HUB_ID>/<DOCKER_IMAGE_NAME>:<TAG> . 
```

### 测试 Docker 图像

在本地机器上启动 Docker 映像进行测试。

```
docker run -p 8080:80 -it <DOCKER_HUB_ID>/<DOCKER_IMAGE_NAME>:<TAG> 
```

Docker 映像中的应用程序现在运行于`localhost:8080`。您可以使用新的 URL 在 [**中运行相同的测试，检查测试输出**](#check-test-outputs) ，您应该会看到与之前相同的测试输出。

准备好之后，您可以按`Ctrl+C`停止测试。

### 向坞站中心推坞站形象

从命令提示符下登录 Docker。出现提示时，输入您的 Docker Hub 密码。

```
docker login --username <DOCKER_HUB_ID> 
```

现在，您可以将之前创建的 Docker 映像推送到 Docker Hub。

```
docker push <DOCKER_HUB_ID>/<DOCKER_IMAGE_NAME>:<TAG> 
```

## 3 - Azure 设置

### 设置 Azure 资源

如果您还没有，请使用 Azure CLI 登录 Microsoft Azure。

```
az login 
```

执行以下命令创建 Azure 资源，并在 Azure 函数上运行推理应用程序。

以下示例使用最低定价层 B1。

用您自己的名字替换下列占位符:

*   `<RESOURCE_GROUP>`
    *   为此应用创建的所有其他 Azure 资源所属的资源组的名称
    *   例如`ResourceGroup`
*   `<LOCATION_ID>`
    *   运行以下命令查看可用位置列表:
    *   `az appservice list-locations --sku B1 --linux-workers-enabled`
    *   例如`centralus`
*   `<STORAGE_ACCOUNT>`
    *   Azure 存储帐户的名称，这是一个用于维护有关您的功能的信息的通用帐户
    *   长度必须在 3 到 24 个字符之间，并且只能包含数字和小写字母
    *   例如`inferencestorage`
*   `<FUNCTION_APP>`
    *   您将创建的 Azure Function 应用的名称
    *   将是默认的 DNS 域，并且在 Azure 中的所有应用中必须是唯一的
    *   例如`inferenceapp123`

#### 创建资源组

```
az group create \
--name <RESOURCE_GROUP> \
--location <LOCATION_ID> 
```

#### 创建存储账户

```
az storage account create \
--name <STORAGE_ACCOUNT> \
--location <LOCATION_ID> \
--resource-group <RESOURCE_GROUP> \
--sku Standard_LRS 
```

#### 创建 Linux 应用服务计划

```
az appservice plan create \
--name <LOCATION_ID> \
--resource-group <RESOURCE_GROUP> \
--sku B1 \
--is-linux 
```

#### 创建应用&从 Docker Hub 部署 Docker 映像

```
az functionapp create \
--resource-group <RESOURCE_GROUP> \
--name <FUNCTION_APP> \
--storage-account <STORAGE_ACCOUNT> \
--plan <LOCATION_ID> \
--deployment-container-image-name <DOCKER_HUB_ID>/<DOCKER_IMAGE_NAME>:<TAG> 
```

#### 配置功能 app

以下假设之前在您的 Docker Hub 档案中上传的 Docker 映像是公开的。如果你已经将它设置为 private，你可以在这里看到[来添加你的 Docker 凭证，这样 Azure 就可以访问图像。](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-function-linux-custom-image#configure-the-function-app) 

```
storageConnectionString=$(az storage account show-connection-string \
--resource-group <RESOURCE_GROUP> \
--name <STORAGE_ACCOUNT> \
--query connectionString --output tsv)  

az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString 
```

### 运行你的 Azure 功能

在前面的命令之后，应用程序通常需要 15-20 分钟才能在 Azure 上部署。你也可以在[微软 Azure 门户](https://portal.azure.com/)的功能应用下看到你的应用。

您的应用程序的网址将为:

`https://<FUNCTION_APP>.azurewebsites.net/api/<FUNCTION_NAME>`

您可以使用新的 URL 在 [**中运行相同的测试，检查测试输出**](#check-test-outputs) ，您应该会看到与之前相同的输出。

### 删除资源组

完成后，删除资源组。

```
az group delete \
--name <RESOURCE_GROUP> \
--yes 
```

请记住，有了 App 服务计划，只要您有资源运行，即使您没有调用该功能，也是要收费的。所以最好在不调用函数时删除资源组，以避免意外的费用。

## 结论

Microsoft Azure Function 似乎是最容易部署的，不需要对我们的推理代码或 ML 库进行任何手动修补。不幸的是，他们的应用服务定价计划像其他非无服务器定价一样工作，并放弃了无服务器架构的主要优势之一，仅在功能运行时为资源付费。但是随着无服务器解决方案的成熟，用不了多久我们就可以像承诺的那样运行 PyTorch 功能了。

如果您知道在无服务器架构上部署 fastai/PyTorch 的其他方法，请在评论中告诉我。

## 参考文献:

[在 Azure 中创建您的第一个 Python 函数(预览)](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-python)

[使用自定义映像在 Linux 上创建函数](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-function-linux-custom-image)

[Azure Functions Python 开发者指南](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-python)