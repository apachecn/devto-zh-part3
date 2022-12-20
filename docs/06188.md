# 从哪里开始—服务结构？

> 原文：<https://dev.to/documentednerd/where-to-i-start-service-fabric-3io9>

因此，容器已经成为现代应用程序开发的重要组成部分。我甚至可以说，容器和微服务对软件开发的影响类似于“面向对象编程”。

现在，我已经与许多使用单片应用程序的人进行了交谈，他们正在寻找一种方法，将他们现有的应用程序分解为一种微服务方法，并支持使用现有基础架构的想法，由于各种原因，他们不一定想要部署在 Linux 上。

现在，基于这种选择，有一种成熟的技术可以利用 docker 容器，并在 windows 环境中编排它们。那就是服务织物。

我发现学习曲线，如果你正在寻找一个整体的应用程序，并把它分解成微服务更容易接受服务结构，它确实有助于你分解你的应用程序，以更好地利用集群中你的计算机上的计算，你仍然可以利用 docker。

如果您正在寻找有关此技术的信息，以下是一些帮助您开始使用服务结构的链接:

概念和架构:

*   [了解微服务](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-overview-microservices)
*   [高级视图](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-content-roadmap)
*   [设计建议](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-scenarios)
*   [花样&做法](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-patterns-and-scenarios)
*   [为什么是微服务？](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-overview-microservices)

服务结构概述:

*   [什么是服务面料？](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-overview)
*   [概述](https://docs.microsoft.com/en-us/azure/service-fabric/)
*   [部署应用程序](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-tutorial-create-dotnet-app)
*   [抬起并移动现有的。网络应用](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-tutorial-create-cluster-azure-ps)
*   [创建和管理集群](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-anywhere)
*   [如何导游](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started)
*   [保护集群](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-tutorial-create-cluster-azure-ps)
*   [构建 web 前端](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-add-a-web-frontend)
*   [开始使用服务结构和。网络核心](https://azure.microsoft.com/en-us/resources/samples/service-fabric-dotnet-core-getting-started/)
*   [托管。服务结构上的网络核心服务](https://blogs.msdn.microsoft.com/dotnet/2016/10/13/hosting-net-core-services-on-service-fabric/)

编码样本:

null-[https://docs . Microsoft . com/en-us/azure/service-fabric/service-fabric-quick start-dot net](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-quickstart-dotnet)

*   [https://github . com/Azure-Samples/service-fabric-dot net-quick start](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)
*   [https://azure.microsoft.com/en-us/resources/samples/?服务=服务结构](https://azure.microsoft.com/en-us/resources/samples/?service=service-fabric)
*   [https://azure . Microsoft . com/en-us/resources/samples/service-fabric-watchdog-service/](https://azure.microsoft.com/en-us/resources/samples/service-fabric-watchdog-service/)
*   [https://azure . Microsoft . com/en-us/resources/samples/service-fabric-dot net-we B- reference-app/](https://azure.microsoft.com/en-us/resources/samples/service-fabric-dotnet-web-reference-app/)
*   [https://azure . Microsoft . com/en-us/resources/samples/service-fabric-dot net-getting-started/](https://azure.microsoft.com/en-us/resources/samples/service-fabric-dotnet-getting-started/)

视频:

null-[https://channel9.msdn.com/events/Build/2017/P4020?term=service%20fabric](https://channel9.msdn.com/events/Build/2017/P4020?term=service%20fabric)

*   [https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric?term=service%20fabric](https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric?term=service%20fabric)
*   [https://channel9.msdn.com/events/Build/2017/B8106?term=service%20fabric](https://channel9.msdn.com/events/Build/2017/B8106?term=service%20fabric)
*   [https://mva.microsoft.com/en-US/training-courses/16925?l=mudwqISGD_6005167344](https://mva.microsoft.com/en-US/training-courses/16925?l=mudwqISGD_6005167344)
*   [https://channel 9 . msdn . com/Shows/Cloud+Cover/Episode-210-Service-Fabric-Series-1-of-3-简介？term=service%20fabric](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-210-Service-Fabric-Series-1-of-3-Introduction?term=service%20fabric)
*   [https://channel 9 . msdn . com/Shows/Cloud+Cover/Episode-211-Service-Fabric-Series-2-of-3-Service-Fabric-with-Containers？term=service%20fabric](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-211-Service-Fabric-Series-2-of-3-Service-Fabric-with-Containers?term=service%20fabric)
*   [https://channel9.msdn.com/Shows/Cloud+Cover/CloudCover212?term=service%20fabric](https://channel9.msdn.com/Shows/Cloud+Cover/CloudCover212?term=service%20fabric)
*   [https://mva.microsoft.com/en-US/training-courses/16747?l=tbuZM46yC_5206218965](https://mva.microsoft.com/en-US/training-courses/16747?l=tbuZM46yC_5206218965)
*   [https://channel9.msdn.com/events/Build/2017/P4168?term=service%20fabric](https://channel9.msdn.com/events/Build/2017/P4168?term=service%20fabric)
*   [https://channel9.msdn.com/events/Build/2017/T6968-R1?term=service%20fabric](https://channel9.msdn.com/events/Build/2017/T6968-R1?term=service%20fabric)

-