# 用 Docker 封装您的 Go 应用程序

> 原文：<https://dev.to/niklasmerz/containerize-your-go-application-with-docker-2f37>

# 用 Docker 将你的 Go 应用容器化

我正在做一个用 Go 写的 API 的小项目。经过一段时间的开发和测试后，我厌倦了构建二进制文件并通过 SFTP 将其上传到测试服务器，所以我开始寻找将项目放入容器并用 CI 工具部署它的方法。在另一个帖子中有更多关于 CI 部分的信息。

经过一些实验，我发现 Jess Frazelle 在许多项目中使用了这样的 docker 文件: