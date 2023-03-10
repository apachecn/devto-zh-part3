# 使用 Azure IoT Edge 和 Azure 认知服务创建图像识别解决方案

> 原文：<https://dev.to/azure/creating-an-image-recognition-solution-with-azure-iot-edge-and-azure-cognitive-services-4n5i>

| 作者 | [戴夫·格洛弗](https://developer.microsoft.com/en-us/advocates/dave-glover)，微软云开发者倡导者 |
| --- | --- |
| 解决办法 | [利用 Azure IoT Edge 和 Azure 认知服务创建图像识别解决方案](https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services) |
| 证明文件 | [自述](https://gloveboxes.github.io/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/) |
| 平台 | [Azure 物联网边缘](https://docs.microsoft.com/en-us/azure/iot-edge/?WT.mc_id=devto-blog-dglover) |
| 证明文件 | [Azure 物联网边缘](https://docs.microsoft.com/en-us/azure/iot-edge/?WT.mc_id=devto-blog-dglover)、 [Azure 定制视觉](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier/?WT.mc_id=devto-blog-dglover)、 [Azure 语音服务](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/overview/?WT.mc_id=devto-blog-dglover)、 [Azure 边缘功能](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-function/?WT.mc_id=devto-blog-dglover)、 [Azure 流分析](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-stream-analytics/?WT.mc_id=devto-blog-dglover)、 [Azure 机器学习服务](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-machine-learning/?WT.mc_id=devto-blog-dglover) |
| 视频培训 | [借助 Azure IoT Edge 实现边缘智能](https://channel9.msdn.com/events/Connect/2017/T253) |
| 程序设计语言 | 计算机编程语言 |
| 日期 | 截至 2019 年 4 月 |

**树莓 Pi 3A+运行 Azure 物联网边缘图像分类器**

[![](img/9cd40d3dc32532dfe719644c5ad91b68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CEL1Zis9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/raspberry-pi-3a-image-classifier.png)

*   [所需零件](#PartsRequired)
*   [树莓派快速安装指南](#QuickInstallationGuideforRaspberryPi)
*   [解决方案概述](#SolutionOverview)
*   [Azure 物联网优势正在发挥作用](#AzureIoTEdgeinAction)
*   [解决方案架构考虑事项](#SolutionArchitecturalConsiderations)
*   [创建水果分类模型](#CreatingtheFruitClassificationModel)
*   [导出 Azure 自定义视觉模型](#ExportinganAzureCustomVisionModel)
*   [Azure 语音服务](#AzureSpeechServices)
*   [了解项目结构](#UnderstandingtheProjectStructure)
*   [构建解决方案](#BuildingtheSolution)
*   [部署解决方案](#DeployingtheSolution)
*   [监控物联网边缘设备上的解决方案](#MonitoringtheSolutionontheIoTEdgeDevice)
*   [从 Azure 物联网边缘刀片监控解决方案](#MonitoringtheSolutionfromtheAzureIoTEdgeBlade)

# 使用 Azure IoT Edge 进行图像分类

有很多图像识别的应用程序，但我在开发这个应用程序时想到的是为视力受损的人在自助结账时扫描水果和蔬菜提供一个解决方案。

## 零件所需

1.  树莓派 3B 或更好，USB 摄像头，和一个扬声器。

    请注意，该解决方案将在 Raspberry Pi 3A+上运行，它具有足够的处理能力，但该设备仅限于 512MB RAM。我会推荐树莓派 3B+,因为它有 1GB 的内存，比旧的 3B 型号更快。Azure IoT Edge 需要 ARM32v7 或更好的处理器。它不会在 Raspberry Pi Zero 中的 ARM32v6 处理器上运行。

2.  或者，你可以在桌面 Linux 上运行这个解决方案——比如 Ubuntu 18.04。该解决方案需要 USB 摄像头通过 Docker 容器以及 Azure IoT Edge 支持。所以现在，这就是 Linux。

## 树莓派快速安装指南

如果您不想下载和构建解决方案，您可以使用我的 [GitHub](https://github.com/gloveboxes?tab=repositories) 存储库中预构建的 Azure IoT Edge 配置，并使用相关的 Docker 映像。

1.  在 Raspberry Pi 上设置 [Raspbian Stretch Lite](https://learn.pimoroni.com/tutorial/sandyj/setting-up-a-headless-pi) 。确保在您的 **wpa_supplicant.conf** 文件中配置正确的[国家代码](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Officially_assigned_code_elements)。
2.  如果你还没有 Azure 账户，那就注册一个免费的 Azure 账户。如果你是学生，那就注册一个 [Azure for Students](https://azure.microsoft.com/en-au/free/students/?WT.mc_id=devto-blog-dglover) 账户，不需要信用卡。
3.  [按照这些说明](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal/?WT.mc_id=devto-blog-dglover)创建 Azure 物联网中心和 Azure 物联网边缘设备。
4.  [在 Raspberry Pi 上安装 Azure IoT Edge 运行时](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-install-iot-edge-linux-arm/?WT.mc_id=devto-blog-dglover)
5.  下载描述此解决方案的 Azure IoT Edge 模块和路由的部署配置文件。打开 [deployment.arm32v7.json](https://raw.githubusercontent.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/master/config/deployment.arm32v7.json) 链接，将 deployment.arm32v7.json 保存在计算机上的已知位置。
6.  为 Azure CLI 命令行工具安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli/?WT.mc_id=devto-blog-dglover) 和[物联网扩展。有关更多信息，请参见](https://github.com/Azure/azure-iot-cli-extension)[使用 Azure CLI 部署 Azure 物联网边缘模块](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-deploy-modules-cli/?WT.mc_id=devto-blog-dglover)
7.  打开命令行控制台/终端，将目录更改为保存 deployment.arm32v7.json 文件的位置。
8.  最后，从命令行运行以下命令，确保替换[设备 id]和[集线器名称]值。

```
az iot edge set-modules --device-id [device id] --hub-name [hub name] --content deployment.arm32v7.json 
```

1.  模块现在将开始部署到您的 Raspberry Pi，Raspberry Pi 绿色活动 LED 将闪烁，直到部署完成。大约 1.5 GB 的 Dockers 模块将在 Raspberry Pi 上下载和解压缩。这是一次性操作。

## 解决方案概述

该系统根据预先训练的机器学习模型识别扫描的商品，告诉人们他们刚刚扫描了什么，然后将交易记录发送到中央库存系统。

该解决方案运行在 Azure IoT Edge 上，由许多服务组成。

1.  **相机捕捉模块**使用相机处理扫描项目。然后，它调用图像分类模块来识别物品，然后调用“文本到语音”模块来将物品标签转换为语音，并且在连接的扬声器上播放扫描的物品的名称。

2.  **图像分类模块**运行 Tensorflow 机器学习模型，该模型已经用水果的图像进行了训练。它负责对扫描的物品进行分类。

3.  **文本到语音模块**使用 Azure 语音服务将扫描的项目名称从文本转换为语音。

4.  USB 摄像头用于捕捉要购买的商品的图像。

5.  用于文本到语音回放的扬声器。

6.  **Azure IoT Hub** (免费层)用于管理、部署和报告运行该解决方案的 Azure IoT 边缘设备。

7.  Azure 语音服务(免费层)用于生成非常自然的语音，告诉购物者他们刚刚扫描了什么。

8.  **Azure 定制视觉服务**用于构建用于图像分类的水果模型。

[![IoT Edge Solution Architecture](img/986211699b06a6c73f8d5454ea67f591.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uiR_86Ed--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/Architecture.jpg)

# 什么是 Azure IoT Edge

该解决方案基于 Azure IoT Edge 构建，后者是 Azure IoT Hub 服务的一部分，用于定义、保护和部署边缘设备的解决方案。它还提供基于云的边缘设备中央监控和报告。

物联网边缘解决方案的主要组件包括

1.  安装在本地边缘设备上的[物联网边缘运行时](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-runtime/?WT.mc_id=devto-blog-dglover)，由两个主要组件组成。负责通信的**物联网边缘“hub”**，负责运行和监控边缘设备上模块的**物联网边缘“agent”**。

2.  [模块](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-modules/?WT.mc_id=devto-blog-dglover)。模块是部署的单位。模块是从诸如 [Azure Container Registry](https://azure.microsoft.com/en-au/services/container-registry/) 或 [docker Hub](https://hub.docker.com/) 这样的注册中心提取的 Docker 映像。模块可以定制开发，构建为 [Azure 函数](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-function/?WT.mc_id=devto-blog-dglover)，或者作为从 [Azure Custom Vision](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-stream-analytics/?WT.mc_id=devto-blog-dglover) 、 [Azure Machine Learning](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-machine-learning/?WT.mc_id=devto-blog-dglover) 或 [Azure Stream Analytics](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-stream-analytics/?WT.mc_id=devto-blog-dglover) 导出的服务。

3.  路线。路由定义了模块之间以及与 Azure IoT Hub 之间的消息路径。

4.  属性。您可以从 Azure IoT Hub 为模块设置“所需”的属性。例如，您可能希望为温度警报设置阈值属性。

5.  创建选项。创建选项告诉 Docker 运行时用什么选项来启动模块。例如，您可能希望为 REST APIs 或调试端口打开端口，定义 USB 摄像头等设备的路径，设置环境变量，或者为某些硬件操作启用特权模式。更多信息参见 [Docker API](https://docs.docker.com/engine/api/latest/) 文档。

6.  [部署清单](https://docs.microsoft.com/en-us/azure/iot-edge/module-composition/?WT.mc_id=devto-blog-dglover)。部署清单将所有内容汇集在一起，并告诉 Azure IoT Edge 运行时要部署哪些模块、从哪里部署、要设置哪些消息路由，以及启动每个模块需要哪些创建选项。

## 蔚蓝物联网边缘在行动

[![iot edge in action](img/59df92dc1b1323c6e154da23a20896eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0RQBjBgM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/iot-edge-in-action.jpg)

## 解决建筑上的注意事项

因此，有了 Azure IoT Edge 的概述，这里是我对该解决方案的考虑和约束。

1.  该解决方案应该从 ARM32v7 上的 Raspberry Pi(运行 Raspbian Linux)扩展到我的桌面开发环境，再扩展到具有工业能力的物联网边缘设备，如那些在[认证的物联网边缘目录](https://catalog.azureiotsolutions.com/)中找到的设备。

2.  该解决方案需要摄像头输入，我使用 USB 网络摄像头进行图像捕捉，因为它支持所有目标设备。

3.  相机捕捉模块需要 Docker USB 设备通过(Windows 上的 Docker 不支持)，所以加上针对 Raspberry Pi 意味着我需要针对 Linux 上的 Azure IoT Edge。

4.  我希望我的开发人员体验能够反映我的目标设备，并且我需要 Docker 对 USB 网络摄像头的支持，所以我在我的 Ubuntu 18.04 开发人员桌面上开发了该解决方案。参见我的针对 Azure 开发者的 Ubuntu 指南。

    作为一种解决方法，如果您的开发设备被锁定到 Windows，那么使用虚拟盒中的 Ubuntu，它允许 USB 设备通过，然后您可以通过虚拟机中的 Docker。有点复杂，但确实有效。

[![raspberry pi image classifier](img/0c721663bbf3e8f514374e43b65fae32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B8m_eun_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/raspberry-pi-image-classifier.jpg)

# Azure 服务

## 创建水果分类模型

Azure Custom Vision 服务是一种创建图像分类机器学习模型的简单方法，无需成为数据科学或机器学习专家。你只需上传多组带标签的图片。例如，你可以上传一组香蕉图片，并将它们标记为“香蕉”。

要创建自己的分类模型，请阅读[How to build a classifier with Custom Vision](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier/?WT.mc_id=devto-blog-dglover)了解更多信息。拥有多种标签图像非常重要，因此请务必阅读[如何改进您的分类器](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier/?WT.mc_id=devto-blog-dglover)。

## 导出 Azure 自定义视觉模型

这个“图像分类”模块包括一个从 Azure Custom Vision 导出的简单水果分类模型。欲了解更多信息，请阅读如何[导出您的模型以用于移动设备](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/export-your-model/?WT.mc_id=devto-blog-dglover)。从项目设置页面中选择一个“**压缩**”域是很重要的，否则您将无法导出模型。

按照以下步骤导出您的自定义 Vision 项目模型。

1.  从自定义视觉项目的**性能**选项卡中，单击**导出**。

    [![export model](img/4e53035f80bced4568ec1985088887cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jbvCvqkw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/exportmodel.png)

2.  从可用选项列表中选择 Dockerfile

    [![export-as-docker.png](img/2fef6298d49f021e5d6924d81c82e583.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GTpwDBn1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/export-as-docker.png)

3.  然后选择 Dockerfile 文件的 Linux 版本。

[![choose docker](img/d6bd9bbe0bb2a50be988cb8b2f898ab6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WpjSPQyk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/export-choose-your-platform.png)

1.  下载 docker 文件并解压缩，您就有了一个现成的带有 Python Flask REST API 的 Docker 解决方案。这就是我在这个解决方案中创建 Azure 物联网边缘图像分类模块的方式。太容易了:)

## 蔚蓝语音服务

[Azure 语音服务](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/overview/?WT.mc_id=devto-blog-dglover)支持“语音到文本”和“文本到语音”。对于这个解决方案，我使用文本到语音(F0)免费层，每月限制为 500 万个字符。您将需要使用 Azure 门户添加语音服务，并从服务中“获取您的密钥”。

[![azure speech service](img/61ec0c5331bf124eeac678b5b1df6e71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V8_pbQoS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/speech-service.png)

打开 deployment.template.json 文件，用从 Azure Speech 服务复制的密钥更新 BingKey。

[![speech key](img/259d0e0d2256dd07d83fbd54fc7faac2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VrPFAA15--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/speech-key.png)

# 如何安装、构建和部署解决方案

1.  克隆这个 GitHub 库。

```
 git clone https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services.git 
```

1.  在你的 Linux 桌面或设备上安装 Azure IoT Edge 运行时(如 Raspberry Pi)。

    按照说明[将您的第一个物联网边缘模块部署到 Linux x64 设备](https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux/?WT.mc_id=devto-blog-dglover)。

2.  安装以下软件开发工具。

    1.  [Visual Studio 代码](https://code.visualstudio.com/)
    2.  另外，以下 Visual Studio 代码扩展
        *   [Azure 物联网边缘](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
        *   [JSON 工具](https://marketplace.visualstudio.com/items?itemName=eriklynd.json-tools)用于改变模块的“创建选项”。
    3.  开发机器上的 Docker 社区版
3.  使用 Visual Studio 代码，将您从 GitHub 克隆的物联网边缘解决方案打开到您的开发人员桌面。

## 了解项目结构

以下描述了项目中突出显示的部分。

1.  有两个模块:CameraCaptureOpenCV 和 ImageClassifierService。

2.  module.json 文件定义了 docker 构建过程、模块版本和 Docker 注册表。更新版本号、将更新后的模块推送到映像注册表以及更新边缘设备的部署清单会触发 Azure IoT Edge 运行时将新模块下载到边缘设备。

3.  构建过程使用 deployment.template.json 文件。它定义了要构建什么模块、要设置什么消息路由以及要运行什么版本的物联网边缘运行时。

4.  deployment.json 文件由 deployment.template.json 生成，是[部署清单](https://docs.microsoft.com/en-us/azure/iot-edge/module-composition/?WT.mc_id=devto-blog-dglover)

5.  项目根文件夹中的 version.py 是一个助手应用程序，您可以在开发机器上运行它来更新每个模块的版本号。版本号的变化很有用，它触发 Azure IoT Edge runtime 来获取更新的模块，并且很容易忘记更改模块版本号:)

[![visual studio code project structure](img/419dd01c698f3b22267f72aab888a655.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x5mQ38Ku--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/visual-studio-code-open-project.png)

## 建筑方案

您需要确保您计划构建的映像与 deployment.template.json 文件中指定的目标处理器架构相匹配。

1.  在 module.json 文件中为每个模块指定 Docker 存储库。如果你使用受支持的 Linux Azure IoT Edge 发行版，比如 Ubuntu 18.04 作为你的开发机器，并且你在本地安装了 Azure IoT Edge，那么我强烈建议你设置一个本地 Docker 注册表。它将显著加快您的开发、部署和测试周期。

    为了原型和测试的目的建立一个本地 Docker 注册中心。

```
docker run -d -p 5000:5000 --restart always --name registry registry:2 
```

1.  如果将映像推送到本地 Docker 存储库，请指定 localhost:5000。

```
"repository":  "localhost:5000/camera-capture-opencv" 
```

1.  确认您计划构建的处理器架构。
    从 Visual Studio 代码底部栏单击当前选择的处理器架构，然后从弹出菜单中选择所需的处理器架构。
    [![](img/010058aa27775172f54f83b0735195da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UG-FGyqg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/select-processor-architecture.jpg)

2.  接下来，通过右键单击 deployment.template.json 文件并选择“**构建并推送物联网边缘解决方案**”来构建并推送解决方案到 Docker。第一次构建会很慢，因为 Docker 需要将基础层拉到您的本地机器上。如果你交叉编译到 arm32v7，那么第一次编译将会非常慢，因为需要编译 OpenCV 和 Python 需求。在快速的英特尔 i7-8750H 处理器上，交叉编译该解决方案大约需要 40 分钟。

    [![docker build and push](img/ecda51e35956079226acfa04affc1103.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vwi9oOS---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/solution-build-push-docker.png)

## 部署解决方案

当 Docker 构建和推送流程完成后，选择您想要部署解决方案的 Azure IoT Hub 设备。右键单击 config 文件夹中的 deployment.json 文件，并从下拉列表中选择目标设备。

[![deploy to device](img/f09f3afc2609ad133fad7a2d5bc176a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--myTYeK4e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/deploy-to-device.png)

## 监控物联网边缘设备上的解决方案

部署解决方案后，您可以使用

`iotedge list`

命令在物联网边缘设备上监控它。

```
iotedge list 
```

[![watch iotedge list](img/48b231c4a3c6a754bcddc80439f38a39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PiR4fYg6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/iotedge-list.png)

## 监控来自 Azure 物联网边缘刀片的解决方案

你可以从 [Azure 门户](http://portal.azure.com)上的 Azure 物联网中心刀片监控 Azure 物联网边缘模块的状态。

[![azure iot edge devices](img/3dac5106b13daeb3c7097dd25aab1792.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DQMv0IUv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/azure-iotedge-monitoring.png)

从 Azure IoT Edge blade 中单击设备，查看有关设备上运行的模块的更多详细信息。

[![azure iot edge device details](img/fd41faf14d2a18c989e223e55193d159.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gqXr7F5U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/azure-portal-iotedge-device-details.png)

# 搞定！

当该解决方案最终部署到物联网边缘设备时，系统将开始告诉您它认为已经扫描的项目。

恭喜您部署了您的首个 Azure 物联网边缘解决方案！

[![congratulations](img/925d5ceba024bd604c866874a693ad34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vdQev0Hm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/gloveboxes/Creating-an-image-recognition-solution-with-Azure-IoT-Edge-and-Azure-Cognitive-Services/raw/master/docs/congratulations.jpg)