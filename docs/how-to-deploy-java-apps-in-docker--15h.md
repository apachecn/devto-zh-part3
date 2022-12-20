# 如何在 Docker 中部署 Java 应用

> 原文：<https://dev.to/zac_siegel/how-to-deploy-java-apps-in-docker--15h>

本文起源于[zsiegel.com](https://zsiegel.com/2018/12/12/how-to-deploy-java-apps-in-docker)

许多 java 开发人员熟悉通过 jar 文件和应用程序容器部署 java 应用程序，例如 [Tomcat](http://tomcat.apache.org/) 但是许多组织现在使用 [Docker](https://www.docker.com/) 容器部署应用程序。让我们看看让 java 应用程序在 Docker 容器中运行的最基本步骤。

## 安装 Docker

为了在容器中运行你的 java 应用，你需要 [Docker](https://www.docker.com/) 。一旦安装好 [Docker](https://www.docker.com/) 将允许你在本地机器上构建和运行你的容器。它运行在许多平台上，包括 Linux、MacOS 和 Windows。前往 Docker 网站，寻找关于[支持的平台](https://docs.docker.com/install/#supported-platforms)的信息。

安装完成后，您可以通过构建和运行`hello-world`容器来测试您的安装。以下输出表明您的安装是成功的。

```
▲ :zsiegel (master)$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
d1725b59e92d: Pull complete Digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1\. The Docker client contacted the Docker daemon.
 2\. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3\. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4\. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/ 
```

## 为你的 Java 应用创建 docker 文件

现在你已经安装了 Docker，让我们用一个由 Gradle 构建的简单 Java 应用程序创建一个 Docker 文件。我们将在容器本身中构建和运行 jar 文件，这为我们提供了一个更加一致的环境。你可以在 Github 上查看我的示例项目，如果你愿意，可以从这个项目开始工作。

现在我们有了基本的 Java 应用程序，让我们看看 Dockerfile 可能是什么样子。

```
# NOTE: This is not a production ready Dockerfile. 
# Utilize this only for development purposes

# Use a container image that has both Gradle and the JDK
FROM gradle:5.0.0-jdk8-alpine

# Switch to the `gradle` user defined by our container image
USER gradle

# Copy over the project directory into the container
COPY --chown=gradle:gradle . /java-and-docker

# Set our working directory to our project directory that we set above
WORKDIR /java-and-docker

# Run the build
RUN gradle build

# Run the jar file
# Since we are using JDK8 we set some additional flags to be more container aware
CMD ["java", "-XX:+UnlockExperimentalVMOptions", "-XX:+UseCGroupMemoryLimitForHeap", "-jar", "build/libs/java-and-docker-1.0.jar"] 
```

## 在 Docker 容器中构建并运行您的 Java 应用程序

现在我们已经创建了 docker 文件，我们可以要求 docker 首先构建容器，然后用下面的命令运行它。

```
docker build -t zsiegel:java-and-docker .
docker run zsiegel:java-and-docker 
```

## 成功

现在，您已经成功地在 Docker 容器中构建并运行了一个 java 应用程序，您可以开始阅读相关文章了。这些将帮助您理解 java 运行时和您的应用程序将如何根据 JVM 版本和您设置的容器标志进行操作。这很重要，因为在容器中运行 java 并不像看起来那么简单。