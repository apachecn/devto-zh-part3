# 什么是容器编排？

> 原文：<https://dev.to/idevkamboj/what-is-container-orchestration-52ab>

容器编排是一个自动化基于容器的应用程序的部署、管理、扩展、联网和可用性的过程

[![dockerHost](img/faaa0d6beae72010dda21fe4d97a47c5.png "dockerHost")](///static/2f310240973323d7b5b30c088bd26143/47007/dockerHost.png)

如果您的应用程序依赖于其他容器，如数据库或消息服务或其他后端服务？如果用户数量增加，您需要扩展应用程序，该怎么办？当负载减少时，您如何缩减规模？为了实现这些功能，您需要一个具有一组资源和功能的底层平台。该平台需要协调容器之间的连接，并根据负载自动伸缩。自动部署和管理容器的整个过程被称为容器编排。Kubernetes 只是一种容器编排技术。如今有多种这样的技术可用。Docker 有自己的工具叫做 **Docker Swarm** ，Google 的**Kubernetes**和 Apache 的**MESOS**。

[![orchestrationTechnologies](img/862bc591040269f7b59ff08a5870315c.png "orchestrationTechnologies")](///static/3dc1f41a3aa2eed01ff1a2c50c7dd064/e334e/orchestrationTechnologies.png)

虽然 [Docker Swarm](https://docs.docker.com/swarm/overview/?source=post_page) 确实很容易设置和开始使用，但它缺乏复杂应用程序所需的一些高级功能。

另一方面，MESOS 很难设置和开始使用，但支持许多高级功能。

Kubernetes 可以说是最受欢迎的，设置和开始有点困难，它提供了许多选项来定制部署，并支持复杂架构的部署。Kubernetes 现在受到所有公共云服务提供商的支持，如 GCP、Azure、AWS，Kubernetes 项目是 Github 中排名靠前的项目之一。

容器编排有多种优势:您的应用程序现在高度可用，因为硬件故障不会使您的应用程序停机，因为我们有多个应用程序实例在不同的节点上运行。

用户流量在各种容器之间实现负载平衡。当需求增加时，可以在几秒钟内无缝部署更多应用实例。当我们耗尽硬件资源时，我们能够在服务级别做到这一点，扩大或减少底层节点的数量，而不必关闭应用程序，并且通过一组声明性对象配置文件轻松完成所有这些工作。这是库伯内特斯。它是一种容器编排技术，用于在集群环境中编排成百上千个容器的部署和管理。