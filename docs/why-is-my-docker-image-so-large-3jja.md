# 为什么我的 Docker 图像这么大？

> 原文：<https://dev.to/jarekprzygodzki/why-is-my-docker-image-so-large-3jja>

让 Docker 图像尽可能小有很多实际好处。但是即使遵循最佳实践

*   使用精简的基础图像
*   利用多阶段构建
*   不要安装你不需要的东西
*   优化构建上下文
*   最小化层数

有些图像最终会变得过大，因为无意中包含了不必要的文件。

# 如何解决图像尺寸问题？

映像文件系统的变化是分层跟踪的。每一层都代表 Dockerfile 中每条指令的文件系统变化。Docker 映像的层本质上是在短暂的中间容器中运行某个命令时生成的文件。

以前我都是执行`docker history <image name>`来查看组成图像的所有图层，手动提取可疑图层并检查其内容。它起作用了，但是很乏味。

# 俯冲

[Dive](https://github.com/wagoodman/dive) 是一个新工具，用于探索 Docker 图像，检查图层内容并发现缩小 Docker 图像大小的方法——所有这些都在一个基于文本的用户界面中完成。

我最近用它来诊断由文件所有权和权限的改变引起的意外图像大小增长。基于`jboss/wildfly`的 docker 文件中的一条指令是`chown -R jboss:jboss /opt/jboss/wildfly/`。看起来很无辜，但是这些文件原来是属于*jbos:root*的。Docker 不知道层内部发生了什么变化，只知道哪些文件受到了影响。因此，这将导致 Docker 创建一个新的层，替换所有这些文件(内容与*/opt/JBoss/wildly/*相同，但拥有新的所有权)，增加数百兆字节的图像大小。

# 资源

*   [潜水 GitHub 页面](https://github.com/wagoodman/dive)
*   [编写 docker 文件的最佳实践](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)