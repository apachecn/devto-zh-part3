# Nvidia ARM64 设备上的 DevOps CI / CD 管道入门

> 原文：<https://dev.to/azure/getting-started-with-devops-ci-cd-pipelines-on-nvidia-arm64-devices-4668>

*我们选择了由[迈克尔·克伦普](https://twitter.com/mbcrump)创造的我们最喜欢的技巧和诀窍，并在整个四月在 Azure 上提供新的技术内容！错过一天(或更多)？[赶上系列](https://dev.to/t/azureapril)。*

**没有天蓝色？** [抢免费订阅](https://azure.microsoft.com/en-us/free?&WT.mc_id=azureapril_devto-blog-cxa)。

[![AzureDevOps-Nvidia](img/922d7882f7f23a42a1f995e928a6c4d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tRSRlypb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8f8ie6bo1y30t3ei31hh.JPG)

在本帖中，我们将介绍如何在 Aarch 64 设备上运行 arm32v7 Azure Pipelines 代理。

随着 [Nvidia Jetson 系列硬件](https://www.developer.nvidia.com/buy-jetson)的发布，计算繁重的处理现在可以在基于 GPU 的小型设备的边缘执行。这为开发物联网解决方案开辟了一个可能性领域，这些解决方案通常依赖外部服务来处理繁重的 AI/ML 工作负载。当与像 [Azure DevOps](https://azure.microsoft.com/en-us/solutions/devops/) 这样的现代 DevOps 解决方案相结合时，我们可以创建强大的管道来构建和安全地向设备交付 GPU 支持的解决方案。

微软的物联网总监最近宣布了一项官方合作伙伴关系，以使用 Nvidia 硬件提供边缘解决方案:

液体错误:内部

使用 Azure DevOps 管道的力量在硬件本身上构建这些解决方案不是很棒吗？

Nvidia Jetson 系列硬件能够通过 Nvidia JetPack 安装程序运行 64 位 Ubuntu 操作系统。我们将假设你已经按照说明将 64 位 Ubuntu 操作系统刷新到你的 Jetson 设备上，并且现在正在通过安装 [Azure Pipelines 代理](https://github.com/Microsoft/azure-pipelines-agent)来利用它作为构建服务器。

截至发稿时，Azure Pipelines 代理没有提供 aarch64 兼容版本。ARM64 支持目前正在进行中，预计将随着 dotnet core 3.0 的发布而推出。与此同时，您可以利用当前可用的 arm32v7 代理和一些步骤来允许您在 aarch64 兼容的基于 Debian 的操作系统上运行 arm32v7 代理。这将允许您使用设备作为 Azure DevOps 中的构建代理，并为任何使用代理的管道提供对底层本机 aarch64 开发环境的完全访问。

为了实现这一点，我们利用了 arm32 二进制文件向后兼容 arm64 架构的事实，前提是满足相关的依赖关系。

首先，我们将使用
`sudo apt-get install gcc-arm-linux-gnueabihf`在我们的设备上安装 arm32 gcc 编译器

这个包将带来一个 armhf 兼容的动态链接器。我们将对其进行符号链接，以便它可以通过默认的 LD_LIBRARY_PATH (/lib) :
`sudo ln -s /usr/arm-linux-gnueabihf/lib/ld-linux-armhf.so.3 /lib/ld-linux-armhf.so.3`

Azure DevOps 管道代理还需要其他模块，因此我们需要确保这些模块在/lib 中也可用:

`sudo ln -s /usr/arm-linux-gnueabihf/lib/ld-linux-armhf.so.3 /lib/ld-linux-armhf.so.3`

`sudo ln -s /usr/arm-linux-gnueabihf/lib/libdl.so.2 /lib/libdl.so.2`

`sudo ln -s /usr/arm-linux-gnueabihf/lib/libpthread.so /lib/libpthread.so`

`sudo ln -s /usr/arm-linux-gnueabihf/lib/libpthread.so.0 /lib/libpthread.so.0`

`sudo ln -s /usr/arm-linux-gnueabihf/lib/libstdc++.so.6 /lib/libstdc++.so.6`

`sudo ln -s /usr/arm-linux-gnueabihf/lib/libm.so.6 /lib/libm.so.6`

`sudo ln -s /usr/arm-linux-gnueabihf/lib/libgcc_s.so.1 /lib/libgcc_s.so.1`

`sudo ln -s /usr/arm-linux-gnueabihf/lib/libc.so.6 /lib/libc.so.6`

`sudo ln -s /usr/arm-linux-gnueabihf/lib/librt.so.1 /lib/librt.so.1`

接下来，我们需要安装 Azure DevOps 管道代理所需的相关外部依赖项，并确保它们是代理所需的 armhf 版本。首先，我们通过以下方式安装 arm32 软件包:

`dpkg --add-architecture armhf`

为了用可用的 armhf 包更新我们的包源，我们运行:

`sudo apt-get update`

然后，我们通过明确请求 armhf 兼容版本来安装必要的依赖项:

`sudo apt-get -y install apt-get install libcurl3:armhf libgtk3-nocsd0:armhf libicu60:armhf`

最后，我们安装了 dotnet 核心 arm32 依赖项:

`wget https://download.visualstudio.microsoft.com/download/pr/10b96626-02d8-415a-be85-051a2a48d0c2/5ec51d3d9f092ba558fb5f1f03d26699/dotnet-sdk-2.1.403-linux-arm.tar.gz`

`mkdir -p $HOME/dotnet && tar zxf dotnet-sdk-2.1.403-linux-arm.tar.gz -C $HOME/dotnet`

`export DOTNET_ROOT=$HOME/dotnet`

`export PATH=$PATH:$HOME/dotnet`

完成这些步骤后，您应该能够继续安装和注册 ARM32 代理:

`export AGENT_VERSION=2.141.2`

`wget -P ~/agent/ https://vstsagentpackage.azureedge.net/agent/${AGENT_VERSION}/vsts-agent-linux-arm-${AGENT_VERSION}.tar.gz`

`tar xzf vsts-agent-linux-arm-${AGENT_VERSION}.tar.gz`

`cd ~/agent/bin`

`sudo ./installdependencies.sh`

`cd ..`

`./config.sh`

最后，通过遵循文档在 Linux 上部署 Azure DevOps 管道代理[，配置代理以连接并注册为 Azure DevOps 实例的构建代理。](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/v2-linux?WT.mc_id=azureapril_devto-blog-cxa)

现在，您应该能够看到您的设备在您的 Azure DevOps 实例中被列为注册代理，并且现在可以使用它在您的 Azure DevOps 管道中使用本机 aarch64 环境构建您的 GPU 驱动的物联网解决方案！

如果您希望从一个紧凑而方便的 shell 脚本运行所有这些步骤，其中还包括安装用于构建自定义管道的物联网边缘开发依赖项，您可以使用以下要点: