# 伙计们，你们不(总是)需要 GPU (OpenVino)🥳

> 原文：<https://dev.to/andrewpierno/guys-you-don-t-always-need-a-gpu-openvino-3o2p>

[![demo](img/c22c1cba4ada1b51577259e3685bf856.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gXCuQwOP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t2u7y7s2uj0qbredb1ct.png)
[![demo 2 ](img/8ff0d8f7bff9e5621030b3103e9de9b8.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--YbgGDtKH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vuzcayrq5clr8xrzg3wq.gif)

这篇文章是由 SugarKubes 赞助的，这是一个面向开发者的容器市场。查看一些我们最新的演示。

当部署深度学习模型时，尤其是在嵌入式系统领域，由于成本或其他考虑因素，您通常没有可用的 GPU。尽管这种情况正在随着谷歌 Coral 或 movidius stick 等专门电路板的出现而迅速改变，但大多数边缘设备都没有任何类型的硬件来处理深度神经网络。输入 OpenVino 以挽救局面。

英特尔的 Openvino 是一个较新的选择，可能适用于您的产品，价格比在您的主板上添加 GPU 低得多。在现代的 I 系列芯片和最近的 Xeons 上，它们有能力通过芯片上的 GPU 运行深度神经网络。而且超级快！你问有多快？

你自己看看吧。旁边带箭头的下拉项目是您可以演示的 Openvino 型号。注意推理时间，它们非常快。其余的延迟是网络和图像处理(您可以停止返回图像以获得更快的推断)。

EC2 上的 open vino
有多快？很高兴你问了。让我们在 AWS 上运行一个实例，看看这些模型运行的速度有多快。

EC2 实例采用英特尔至强处理器(或部分处理器)。因此，我将构建一个 T2.Medium。您可以在上面运行 Openvino 模型，直接从云中运行，处理时间相当不错！或者，如果您足够幸运，拥有搭载英特尔处理器(如 NUC 系列)的 x86 主板，它也可以成为一款出色的嵌入式解决方案。

# 启动一个新的 EC2 实例并加入进来！

# 我将登上 T2。中等

ssh ubuntu@whatever
为了运行模型，我们将安装 Docker 并登录 SugarKubes hub。如果你只是想看看演示，你可以在这里。我还创建了一个 OpenVino 基础映像，这样你可以更快地自己构建和部署 OpenVino 模型，它可以在 Github 和 Docker Hub 上获得。

sudo apt 更新
sudo apt install dock . io

# 登录 sugar 注册表

码头登录日志苏加库比我

# 运行行人检测容器！不要忘记对外开放港口！

docker run-RM-DTI \
-p 9090:9090 \
-p 9001:9001 \
registry . sugarkubes . io/sugar-cv/Intel-行人检测:最新

就是这样！我们现在有了一个深度学习行人检测器，在一个简单的 EC2 实例上运行在云端。区别？该模型在 CPU 上的推理时间小于 50 毫秒。太疯狂了。这个容器是无状态的，所以把它放到一个自动伸缩组或者一个 Kubernetes 集群上，你就再也不用碰它了。

SugarKubes 上可用的英特尔 OpenVino 模型
GKE 上的 OpenVino、GC、GCR 上的 OpenVino
GKE 上的 open vino 以及 GC 计算实例将工作良好。他们有可用的 Xeon Skylake 处理器！

我痴迷于谷歌的新云运行。你可以把它指向一个容器，它会管理自动缩放，并自动通过 https 提供服务。然而，令人惊讶的是，我无法让 OpenVino 模型在上面运行，并且它们没有提供一种方法来跳进我已经看到的容器中。随着该服务退出测试，有望运行 OpenVino 模型，因为这将是🔥。

希望你喜欢这篇文章！如果你有兴趣了解更多关于 SugarKubes 上可用容器的信息，请考虑加入我们的邮件列表。

如果你对获得免费容器感兴趣，邀请 10 个人并获得你选择的容器，最高 100 美元！