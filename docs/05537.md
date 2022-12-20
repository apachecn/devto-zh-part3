# 应用程序编排工具简明指南

> 原文：<https://dev.to/bovermyer/a-concise-guide-to-application-orchestration-tools-34hn>

所以这是你的情况。您希望将应用投入生产，并以代码形式管理基础设施。对此有三个好的选择。但是你应该选择哪一个呢？有特定于云供应商的选项，但我在这里只讨论供应商中立的选项。

基本上，你有三个主要的选择。他们是 Docker Swarm、Kubernetes 和 Hashicorp Nomad。

如果你需要管理虚拟机和容器，那么使用 [Hashicorp Nomad](https://www.nomadproject.io/) 。它是为混合环境设计的。

如果您有一个小环境或一组纯容器化的环境，请使用 [Docker Swarm](https://docs.docker.com/engine/swarm/) 。使用简单，可靠。

如果您有一个需要复杂编排规则的大型容器环境，请使用 [Kubernetes](https://kubernetes.io/) 。它功能强大，可以很好地处理边缘情况。