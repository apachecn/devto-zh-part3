# AWS 上的第一个 Windows 容器服务

> 原文：<https://dev.to/duplocloud/first-windows-container-service-on-aws-2088>

这个周末，在创建 EC2 主机时，我兴奋地看到菜单中的“Windows Server 2016 with Containers”选项。微软本月早些时候推出了 Server 2016 操作系统，现在 AWS 也推出了对该操作系统的支持。我现在可以自豪地说，Duplo 是 AWS 上的第一个 windows 容器服务。我要祝贺微软和 Docker 在 Linux 中成功实现了与 Docker 的 API 对等。

Duplo 的容器管理组件总是支持“分配标签”的概念，因此添加对 Windows 容器的支持是一件轻而易举的事情。我希望我能写更多关于在 Duplo 中添加 windows 容器管理支持的内容，但事实是没有太多内容可写。只花了一个晚上的时间，大部分时间都花在了用 duplo 代理烘烤 AMI，暴露 UI 选项等后勤工作上。而不是任何核心代码更新。观看下面的演示。

[https://youtu.be/XOvHqLtRD-Q](https://youtu.be/XOvHqLtRD-Q)

AWS 中的 ECS 服务也有望在 2016 年底前发布支持，正如他们在[博客](http://aws.amazon.com/blogs/aws/run-windows-server-2016-on-amazon-ec2/)中所述。以 Azure 为例，我一直对他们的容器管理故事感到困惑，他们说他们同时支持 Mesos 和 Docker Swarm。我还听说他们考虑支持 Kubernetes。但是我想知道在公共云中，为什么客户会关心哪个容器编排软件创建了他的容器。除了调度容器之外，重要的部分似乎是服务与云生态系统的集成，如 IAM、LB、DNS、存储等。那么微软真的打算用这三个容器管理解决方案做这个生态系统集成吗？他们为什么不像 AWS 那样做呢，即创建他们自己的、在他们的云中本地工作的产品。随着未来更多细节的出现，了解更多关于这一策略的信息将会非常有趣。

AWS 上的第一个 Windows 容器服务的帖子[最早出现在](https://duplocloud.com/blog/first-windows-container-service-on-aws/) [DuploCloud](https://duplocloud.com) 上。