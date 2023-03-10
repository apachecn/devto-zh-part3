# 走向无服务器:第 2 部分谷歌云运行

> 原文：<https://dev.to/niklasmerz/go-serverless-part-2-google-cloud-run-2jo8>

# 在容器中运行无服务器功能

## 什么是云跑和 Knative？

Google Cloud Run[最近在 Google Cloud Next](https://cloud.google.com/blog/products/serverless/announcing-cloud-run-the-newest-member-of-our-serverless-compute-stack) 上宣布，经过一个私人的 alpha 阶段，我们终于能够谈论它了。云运行基本上允许我们通过容器部署无状态 HTTP(S)端点，而无需管理服务器或 Kubernetes 集群。现在，我们的 Docker 容器有了一个无服务器平台，定价和部署与大多数无服务器平台一样简单。一切都得到管理，如扩展容器，路由流量，自定义域和 HTTPS 证书。

Cloud Run 是建立在 Knative 之上的，kna tive 是一个基于 Kubernetes 的开源项目，它让我们相信我们的代码/容器仍然是高度可移植的。

查看[谷歌文档](https://cloud.google.com/run/docs/concepts)了解云运行和 Knative 背后的概念。

## 为 Go 构建 Docker 容器

Cloud Run 需要我们的项目注册中心中的 Docker 映像来部署服务及其修订版。

像[第 1 部分](https://blog.merzlabs.com/posts/go-serverless-1)一样，我们使用项目 [Gistdirect](https://github.com/NiklasMerz/gistdirect) 来尝试 Google Cloud Run。这个函数提供一个简单的重定向服务。

[![niklasmerz image](img/2cc76f01de0486fe1449ca47dd4e06f7.png)](/niklasmerz) [## 走向无服务器:第 1 部分谷歌云功能

### niklas Merz Apr 12 ' 194 分钟阅读

#go #serverless #googlecloud #googlecloudfunctions](/niklasmerz/go-serverless-part-1-google-cloud-functions-2i35)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [尼克拉斯梅尔兹](https://github.com/NiklasMerz) / [吉斯直接](https://github.com/NiklasMerz/gistdirect)

### 重定向至带有主机文件的 gist 的 URL。为无服务器和容器做好准备

<article class="markdown-body entry-content container-lg" itemprop="text">

# gistdirect

## Gistdirect 是一个简单的 URL 重定向服务/ URL 缩短器。

> 想象一下 bitly.com 或 goo.gl 和你的主机文件的组合

[![Run on Google Cloud](img/3b5f9f594f50b4dffec154b01210744d.png)](https://console.cloud.google.com/cloudshell/editor?shellonly=true&cloudshell_image=gcr.io/cloudrun/button&cloudshell_git_repo=https://github.com/niklasmerz/gistdirect)

创建一个 Github Gist 或者任何一个普通的文件。Gistdirect 将获取文件并将别名重定向到完整 url。

示例“主机”文件:

```
gh https://github.com 
```

[测试文件](https://gist.github.com/NiklasMerz/a9b5905f742b5863197a0af0465a39f6)

文件的 URL 作为环境变量提供:`GIST_URL`

这是一个简单的 Go 功能，很容易在任何无服务器平台上运行。您也可以在某个地方运行二进制文件，它将在端口 8080 上侦听。可执行文件从[版本](https://github.com/NiklasMerz/gistdirect/releases)开始提供。

我用谷歌云功能和 Firebase 主机运行了它。您可以通过 web 界面或 gcloud CLI 从“function.go”部署该功能。Firebase 可用于将一个[自定义域](https://firebase.google.com/docs/hosting/custom-domain)分配给该功能。Firebase CLI 和“firebase.json”文件帮助您将该功能分配给根…

</article>

[View on GitHub](https://github.com/NiklasMerz/gistdirect)

我们需要一个 Docker 容器来运行我们的 Go 函数。Gistdirect 有一个类似于这个 [Dockerfile](https://blog.merzlabs.com/posts/docker-go) 的 Dockerfile，但做了修改以使用 [Go 模块](https://github.com/golang/go/wiki/Modules)，因为 Gistdirect 的结构是使用模块:

```
FROM golang:alpine as builder

ENV PATH /go/bin:/usr/local/go/bin:$PATH
ENV GOPATH /go

RUN apk --no-cache add gcc g++ make ca-certificates

COPY . /home/gistdirect

RUN set -x \
    && cd /home/gistdirect/main \
    && go build \
    && mv main /usr/bin/gistdirect \
    && rm -rf /go \
    && echo "Build complete."

FROM alpine:latest

RUN apk add --no-cache \
    ca-certificates

COPY --from=builder /usr/bin/gistdirect /usr/bin/gistdirect

EXPOSE 8080
ENTRYPOINT ["gistdirect"]
CMD [""] 
```

这个 Docker 文件构建了使用`main()`函数中的重定向处理程序的`main.go`文件，并使其可执行到我们将运行的最终 Docker 映像。

要为云运行准备 Dockerfile，我们需要有`gcloud` CLI 工具，并且必须登录到启用计费的项目(参见[第 1 部分](https://blog.merzlabs.com/posts/go-serverless-1))。如果您遵循了第 1 部分，请使用相同的项目来重用您的域。我们可以将我们的项目提交到 Google Cloud Build，它会构建图像并将其放入我们项目的注册表中。

```
gcloud builds submit --tag gcr.io/[PROJECTID]/gistdirect 
```

## 部署到云运行

使用谷歌云控制台创建服务非常简单。只需转到云运行，“创建新服务”，选择图像，设置环境变量，我们就可以开始了。

当服务准备好了，我们得到一个这样的 URL:`gistdirect-khsgfjhsg-uc.a.run.app`这就是我们的功能。

## 获得更好的域名

如果你完成了第 1 部分，你可能已经建立并验证了这个项目的一个域。可以去云运行主页面选择“管理自定义域”。在这里，您可以使用之前添加的域，设置另一个自定义域并将其映射到您的服务。一旦 HTTPS 准备好了，并且您在 DNS 中做了 CNAME 更改，您就可以调用您的容器了。