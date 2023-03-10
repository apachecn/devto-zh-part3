# 使用 DevOps 从 ISO 到 Azure 中的 VHD

> 原文：<https://dev.to/garvinc/from-iso-to-vhd-in-azure-using-devops-4241>

**更新**
回购已更新为使用 Github 动作。自述文件已更新以反映这一点。

您是否曾经不得不从头开始手动安装操作系统来创建用于云中的基本映像？由于修补和更新，您是否需要定期重建这些基本映像？你正在开发一个打包插件吗？这篇博文介绍了一个端到端的 DevOps 管道，用于从操作系统安装盘创建 ISO 格式的基本映像。

这将如何运作？
这个过程依赖于 Azure 中可用的[嵌套虚拟化](https://azure.microsoft.com/en-us/blog/nested-virtualization-in-azure/)特性。首先，将创建一个能够运行嵌套虚拟机的生成器虚拟机。然后，在 builder VM 中，将使用 packer 来创建基本映像。一旦映像创建过程完成，映像将从构建器虚拟机复制到 Azure 存储。从那里，这些映像可以用来创建虚拟机或用作基于 packer [Azure Builder](https://www.packer.io/docs/builders/azure.html) 的独立管道中的源映像。

[![ISO to VHD in Azure](img/27b6f70ab8d13a080a22ae4e28bb950e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wgh_Cb8T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6o1v0165gxmkqyn9t3z3.png)

需求
要完全自动化这个过程，需要一些不同的工具。如果你对它们都不熟悉，在继续之前阅读一下会有所帮助。

*   创建一个免费的 Azure DevOps 帐户,如果你还没有的话。然后按照文档创建一个[基本管道](https://docs.microsoft.com/en-us/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav)。
*   如果您还没有 Azure 订阅，请注册一个 Azure 免费试用版。
*   该过程依赖于 packer 来构建图像，因此请访问[入门](https://www.packer.io/intro)页面来熟悉该工具。具体来说，是基于 [Qemu](https://www.packer.io/docs/builders/qemu.html) 的映像构建器。
*   映像构建器将是一个使用[云初始化](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/using-cloud-init)配置的 Ubuntu 虚拟机。如果你不熟悉 cloud init，请花点时间向[了解一下](https://cloudinit.readthedocs.io/en/latest/)。
*   打包程序创建的嵌套虚拟机将使用 [kickstart](https://docs.centos.org/en-US/centos/install-guide/Kickstart2/) 文件自动安装操作系统。在这种情况下，我选择了 [Centos](https://www.centos.org/download/) 作为基本操作系统。

这个管道的源代码存储库将包含一组 bash 脚本和配置构建器和嵌套虚拟机所需的配置文件。请从我的 [Github](https://github.com/garvincasimir/Azure-ISO-To-VHD) 账户下载并上传到你的 Azure DevOps 库。您还可以选择使用 [Github 存储库](https://docs.microsoft.com/en-us/azure/devops/pipelines/repos/github?view=azure-devops)作为 DevOps 管道的来源。

此时，您的存储库中应该有以下文件和目录布局
[![ISO to VHD In Azure](img/66ff13cf404192c65485c890bb84f549.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hert4L9W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1xuywpakul21w3i5swc3.png)

**Builder 虚拟机**
*cloud-init . YAML*用于配置 Ubuntu Builder 虚拟机。这个虚拟机将在 Azure market place[中使用最新的 Ubuntu 映像创建。cloud init 文件还将包含 base64 表示的 bash 脚本，这些脚本是安装 packer、构建依赖项和配置文件所必需的。请记住，该文件仅用作模板。 *gen-init.sh* 脚本用文件的 base64 表示替换 *cloud-init.yaml* 中的所有文件路径，并将结果保存到 *cloud-init-gen.yaml* 。请将此文件添加到您的*中。gitignore* ，因为它应该在构建过程中生成。](https://azuremarketplace.microsoft.com/en-au/marketplace/apps/Canonical.UbuntuServer)

*packer-build.sh* 脚本将用于构建映像。它将使用 Azure CLI [run-command](https://docs.microsoft.com/en-us/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) 特性从 Azure DevOps 运行。这个特性允许您在没有 SSH 连接的情况下在 Azure vm 中运行命令。点击了解更多关于运行命令[的信息。](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/run-command)

**管道**
构建管道由 4 个代理作业组成。

*   *设置*任务包含负责创建构建器虚拟机的任务。
*   *构建*任务包含负责运行 packer 的任务。
*   *上传*任务将完成的 VHD 上传到 Azure 存储。
*   可选的*测试从 VHD 创建虚拟机*作业从上传的 VHD 创建一个测试虚拟机。

将每个作业配置为依赖于前一个作业是非常重要的。否则 Azure DevOps 可能会运行它们。从技术上讲，这些任务都可以在一个代理作业中运行。但是，它们被分开以保持在自由层中托管代理上允许的 30 分钟运行时间内。

[![Pipeline Setup](img/842b0cfd59b903721e025ccd6f8f8491.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gIKM8QXA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bul6ccsjzjzjs0k4bxuh.png)

请执行以下操作:

*   [创建存储帐户](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-portal)，该帐户将用于上传完成的基础映像。

*   创建上面提到的 4 个代理作业。确保将每个作业配置为依赖于前一个作业。

*   将以下变量添加到生成管道中。请包括所有不是系统变量的内容。[![pipeline variables](img/4cd9c4b43d693bd4cb4779af2d9017db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3FPOGaKX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6s1ssm9qkpv80z7rkp15.png)

您选择的资源组名称不应该已经存在。 ****如果存在**** ，脚本将删除资源组。映像上传到存储后，就不再需要资源组和其中的所有资源了。删除资源组将删除该组及其所有资源。请不要向该组部署任何其他资源。选择可专用于此管道的名称。CLI 任务中使用的 Azure 连接应具有删除/创建资源组、托管映像、存储 SAS 令牌和虚拟机的权限。

***部署构建器虚拟机***

*   在*设置*作业中创建一个新的 Azure CLI 任务，并将其命名为 *Deploy Builder VM to Azure* 。
*   将任务配置为运行 *create-inception.sh* 脚本，并包含之前创建的变量作为参数。![deploy builder vm](img/7a4d730119e245d803d3d81897c5b86e.png)

**运行封隔器建造**

*   在*构建*作业中创建一个新的 Azure CLI 任务，并将其命名为*在构建器虚拟机上运行打包器构建*。
*   配置任务以执行 *run-packer-build.sh* 脚本，并包含之前创建的变量作为参数。![Run packer build](img/b132790fba09a3736b789cb085b10b54.png)

**VHD 上传**

*   在*上传*作业中创建一个新的 Azure CLI 任务，并将其命名为*运行上传 VHD* 。
*   配置任务以执行 *run-upload-vhd.sh* 脚本，并包含之前创建的变量作为参数。![Upload VHD](img/28a8a23c4f4b1f91583fda99425fa639.png)

**创建一个测试虚拟机**

*   在*测试从 VHD* 作业创建虚拟机中创建一个新的 Azure CLI 任务，并将其命名为*创建测试虚拟机*。
*   将任务配置为执行 *create-test-vm.sh* 脚本，并包含之前创建的变量作为参数。![Upload VHD](img/b0d47082b4bf09ac3a6eaa82ce8258ed.png)

**故障排除**
如果您的映像由于某种原因没有建立，我建议使用远程桌面进行故障排除。该脚本配置映像构建器虚拟机以启用 RDP 访问，从而实现无摩擦故障排除。

*   微软基于 osx 的 RDP 客户端似乎是响应速度最快的。
*   从构建器虚拟机桌面启动基于根的终端，并运行 */root/packer-buil.sh* 构建脚本。
*   注意控制台输出中的 vnc 连接信息。例如 vnc://127.0.0.1:5909
*   打开远程查看器并输入 VNC 连接信息
*   现在，您可以看到正在构建的虚拟机并与之交互
*   您还可以使用-debug 标志运行 packer，这样当您手动与嵌套的 vm 交互时，它会在每一步暂停
*   如果预装的浏览器不能正常工作，安装[实时 VNC 浏览器](https://www.realvnc.com/en/connect/download/viewer/)。

**总结**
如果一切顺利，您应该拥有一个基于您管道中生成的定制 VHD 的虚拟机。当您对结果满意时，创建一个负责删除资源组的 Azure CLI 任务。我将导出的管道和本文中提到的所有脚本发布到我的 [github](https://github.com/garvincasimir/Azure-ISO-To-VHD) 库。请随意提出问题并做出贡献。