# Docker 无处不在不是一件好事。

> 原文：<https://dev.to/louhdetech/docker-everywhere-is-not-a-good-thing-4i>

Docker 是一种趋势，每个人都想成为其中的一部分，但不是以一种好的方式。这种趋势的不好的一面是，一切都在容器中进行，如 web 应用程序或数据库，甚至 GitLab，这不利于数据的持久性、安全性，在某些情况下可能还会影响性能。

依我看，一个解决方案是使用像 Swarm 或 Kubernetes 这样的 orchestrator，仔细研究所有容器需要哪种容量。你也可以通过一个好的反向代理和对 Kubernetes 使用 RBAC 来提高安全性。

你有什么方法来保证你的数据安全，使容器安全，并在你的网站上有良好的表现？