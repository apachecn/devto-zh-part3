# quartus 的轻量级无服务器 java 函数

> 原文：<https://dev.to/nheidloff/lightweight-serverless-java-functions-with-quarkus-4ejd>

Quarkus 是一个“下一代 Kubernetes 原生 Java 框架”,可以开源获得。Quarkus 承诺快速启动和低内存使用。这使得 Quarkus 非常适合在 Kubernetes 上作为微服务运行的 Java 工作负载，以及作为无服务器功能运行的 Java 工作负载。

阅读文章[为什么夸尔库斯](http://in.relation.to/2019/03/08/why-quarkus/)了解夸尔库斯是如何工作的。简而言之，Quarkus 通过 [GraalVM](https://graalvm.org/) 在本地二进制文件中编译 Java 源代码。Quarkus 框架帮助开发人员轻松构建能够利用 GraalVM 优势的应用程序。

现在大多数开发人员使用 JavaScript/Node.js 来构建无服务器功能。一个原因是 Node.js 应用程序通常(曾经？)启动更快，消耗内存更少。由于您通常需要为云中的内存和时间付费，Node.js 通常可以为您节省资金并提供更好的体验。

今天，我简要介绍了如何使用 Quarkus 构建轻量级的快速 Docker 映像，这些映像可以作为无服务器功能在基于 Apache OpenWhisk 的平台上运行，如 T2 IBM Cloud Functions T3。OpenWhisk 的一个很大的特点就是可以使用 Docker 开发函数。

我在 GitHub 上整理了一个小的[样本](https://github.com/nheidloff/openwhisk-quarkus-starter)。

以下是如何在 IBM 云函数上尝试 Quarkus 的方法。你所需要的只是一个免费的 IBM Cloud lite 账户和 T2 的 IBM Cloud lite。

先下载代码:

```
$ git clone https://github.com/nheidloff/openwhisk-quarkus-starter.git
$ cd openwhisk-quarkus-starter 
```

在构建实际的 Docker 映像之前，会创建本机二进制文件。遵循 Quarkus 网站上的[说明](https://quarkus.io/guides/building-native-image-guide)如何设置 GraalVM、Java JDK 和 Maven。

然后构建映像(用您的 Docker 名称替换‘nheidloff’)。

```
$ mvn package -Pnative -Dnative-image.docker-build=true
$ docker build -t nheidloff/quarkus-serverless:1 .
$ docker push nheidloff/quarkus-serverless:1 
```

为了在本地调用该功能，运行以下命令:

```
$ docker run -i --rm -p 8080:8080 nheidloff/quarkus-serverless
$ curl --request POST \
  --url [http://localhost:8080/run](http://localhost:8080/run) \
  --header 'Content-Type: application/json' \
  --data '{"value":{"name":"Niklas"}}' 
```

为了更改示例函数的实现，请使用您最喜欢的 Java IDE 或文本编辑器。当您运行以下命令时，应用程序将在您每次保存文件时自动更新:

```
$ mvn compile quarkus:dev 
```

可以使用这些命令在 IBM Cloud 上创建 OpenWhisk 函数:

```
$ ibmcloud login
$ ibmcloud fn action create echo-quarkus --docker nheidloff/quarkus-serverless:1 -m 128 
```

**注意:**部署的 Quarkus 功能只消耗 128 MB 内存。我觉得这很神奇。

调用该命令最简单的方法是通过 ibmcloud CLI:

```
$ ibmcloud fn action invoke --blocking echo-quarkus --param name Niklas 
```

接下来，我想更多地了解夸尔库斯函数的速度。