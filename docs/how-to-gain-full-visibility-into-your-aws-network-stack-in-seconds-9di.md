# 如何在几秒钟内全面了解您的 AWS 网络堆栈？

> 原文：<https://dev.to/totalcloudio/how-to-gain-full-visibility-into-your-aws-network-stack-in-seconds-9di>

您如何解决 AWS 中的错误配置或 AWS 网络问题？您平均花费多少时间来调试问题？如果超过一个小时，你可能用错了工具！

> ***人类天生具有视觉。一项重大研究表明，一个*** [***人脑处理图像的速度比处理文本***](http://www.t-sciences.com/news/humans-process-visual-data-better) ***快 6 万倍。此外，传输到大脑的 90%的信息是视觉的。因此，与表格或仪表板样式的调试工具相比，可视化提示有助于更快地调试任何错误配置。即使在最“超复杂”的云环境中。***

此外，云提供商的新服务带来了更多的抽象。这种服务可能会隐藏它们上面网络层的元素。因此，要从上下文理解资源连接或从粒度上理解这些资源性能，可视化控制台更合适。

例如，你想检查 VPC 内部的资源连接。为此，使用表格格式或几个仪表板很麻烦。像 cloud mapper 这样的工具确实提供了拓扑视图，但是这些服务不能在多个级别上提供端到端的上下文。然而，可以映射和显示资源之间的实时关系的可视化控制台可以帮助您理解整个拓扑结构；此外，节省你很多努力。

为了演示这一点，让我们考虑一个简单的 web 服务器架构，如下所示。该架构是基本的，有一个实例和几个跨两个地区的 S3 桶。

[![A simple web server architecture ](img/6fc6aa7e46cead1d9177884db8ad21c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4-H-mp-G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mw985hix0n05fra796gn.png)

对于像这样简单的架构来说，以表格或仪表板的形式找到一个区域中的所有子网和资源关系是一项挑战。随着应用程序复杂性的增加，其架构也随之增加。

**这里是 *TotalCloud 网络视图*相同架构的视频。TotalCloud 的交互式可视控制台在单个窗格上显示所有子网分布和资源之间的关系。**

[https://www.youtube.com/embed/1po56QMlWyA](https://www.youtube.com/embed/1po56QMlWyA)

**从上面的视频中，您可以观察到沉浸式视觉控制台有助于您更轻松地理解任何架构。事实上，比表格或可视化仪表板快 100 倍。对于像 AWS 这样的动态环境，这样的视图提供了完整的云可见性。**T3】

除了查看所有子网，您甚至可以深入到每个子网，检查路由表和 NACLs 是否配置正确。这展示了网络的焦点视图。这有助于您在几秒钟内克服 AWS 设计故障，并在多个层面识别任何盲点。

## 【TotalCloud AWS Network View 可以帮助您快速解决的一些使用案例有:

*   **查找可访问互联网的资源，反之亦然:**发现影响 AWS 安全状态的开放端口或错误配置。
*   **了解不同子网之间的可访问性:**根据上下文了解 VPC 中的子网关系，以便更好地进行网络分析。
*   **分析路由表和 NACL 配置:**实时查看每个子网配置的路由表和 NaCl，以查看 AWS 安全性。
*   **查找受影响的资源:**利用上下文信息在实时拓扑上观察网络组件，以便更快地发现设计故障或安全威胁。
*   **查找与子网相关联的公共可访问资源:**查看每个 VPC 内的所有子网，并深入查看每个子网内的公共资源。

> ### [**Register for a 30-day free trial and watch all these live scenes.**](https://totalcloud.io/user-signup.html)

## T1】结论 :

维护配置良好的 AWS 网络是云用户的责任。TotalCloud 通过其集中视图、快速实时调试功能和丰富的拓扑可视化帮助轻松实现这一目标。

如果你想一目了然地了解 AWS 的安全状况， [阅读这篇文章](http://blog.totalcloud.io/reinforce-aws-security-posture-multiple-levels-seconds/)。

注:最初发布于 2018 年 12 月 4 日 [blog.totalcloud.io](https://blog.totalcloud.io/gain-full-visibility-aws-network-stack-seconds/) 。