# 您首次部署到 Google Cloud Run

> 原文：<https://dev.to/markoa/your-first-deploy-to-google-cloud-run-39i>

谷歌在本月的 Cloud Next’19 大会上推出了 [Cloud Run](https://cloud.google.com/run/) ，这是一种基于 Docker containers 运行无服务器应用的新解决方案。我们现在可以说的是，这是无服务器计算的重要一步——部署到云运行比在 Kubernetes 上运行容器容易得多。它也没有 Lambda 函数所具有的架构限制。 [Semaphore](https://semaphoreci.com) 提供无缝的 CI/CD 管道来构建、测试和部署应用到 Google Cloud Run。

## 什么是 Google Cloud Run？

Google Cloud Run 是一个完全托管的平台，它采用 Docker 容器映像，并将其作为无状态、自动扩展的 HTTP 服务来运行。

Cloud Run 与第一代无服务器平台(如 AWS Lambda、Google Cloud Functions 或 Azure Functions)的区别在于，它允许你运行服务于多个端点的任意应用程序，而不是具有特定接口的小功能。

Cloud Run 基于 [Knative](https://www.knative.dev/) ，这意味着类似的解决方案将可能出现在其他托管的 Kubernetes 平台上。

## 我在 Google Cloud 上运行的项目会运行吗？

Google 已经发布了一个[容器运行时契约](https://cloud.google.com/run/docs/reference/container-contract)，其中列出了对容器的要求，包括:

*   该容器是为 64 位 Linux 编译的。
*   容器在端口 8080 上侦听 HTTP 请求。
*   最高可容纳 2 GB 内存。
*   容器实例必须在收到请求后 4 分钟内启动 HTTP 服务器。
*   当容器从 0 自动缩放到多个运行实例时，您的应用程序应该可以工作。
*   所有的计算都是无状态的，并且只限于一个请求。
*   只要你的项目符合上面的一般要求，你就可以在 Cloud Run 上运行用任何编程语言编写的任何应用。

请注意，云运行目前处于测试阶段，因此这些要求可能会随着时间的推移而变化。

## 没有工作流程——这是一件好事

已经熟悉 Docker 或 Heroku 等传统 PaaS 解决方案的开发人员将会对 Google Cloud Run 如鱼得水。

一旦您的应用程序被打包到 Docker 中，您需要做的就是:

*   将容器图像推送到 Google 容器注册表。
*   运行`gcloud beta run deploy`

几分钟之内，Cloud Run 将在您可以自定义和公开的域下提供新的应用程序。

## 示例:使用信号量的连续部署

在下面的演示中，我们将为微服务配置一个带信号量的无服务器 CI/CD 管道，该微服务将执行以下任务:

*   运行自动化测试；
*   构建 Docker 容器；
*   将容器图像推送到 Google 容器注册表；
*   提供一键式手动部署到分阶段 Google Cloud 运行环境；
*   在主 Git 分支上每次成功构建之后，自动部署到生产云运行环境。

[![CI/CD pipeline with Google Cloud Run](img/0bb83b63dd81ade21a3610a7eeb44804.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ShLnyGot--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uu32qd4ytqv20cewu3yv.png)

您可以在 GitHub 上找到该项目的完整源代码:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [信号量演示](https://github.com/semaphoreci-demos) / [信号量演示云运行](https://github.com/semaphoreci-demos/semaphore-demo-cloud-run)

### 一个信号量 CI/CD 管道与 Google Cloud 一起运行。

<article class="markdown-body entry-content container-lg" itemprop="text">

# Google Cloud Run 的信号量 CI/CD 演示

这是一个示例应用程序和 CI/CD 管道，展示了如何使用 Semaphore 2.0 构建、测试和部署一个无服务器的微服务到 Google Cloud Run。

成分:

*   Ruby Sinatra 作为 web 框架
*   用于测试的 RSpec
*   包装在码头集装箱中
*   将容器推送到 Google 容器注册表
*   部署到谷歌云运行
    *   一键式部署到暂存
    *   在主分支机构的绿色基础上持续部署到主分支机构

## 信号量上的 CI/CD

如果您不熟悉 Semaphore，可以随意派生这个存储库并使用它来[创建一个项目](https://docs.semaphoreci.com/article/63-your-first-project)。

CI/CD 管道在`.semaphore`目录中定义，如下所示:

[![CI/CD pipeline on Semaphore](img/824ea80f846d27b8274776fca3811a59.png)](https://raw.githubusercontent.com/semaphoreci-demos/semaphore-demo-cloud-run/master/pipeline.png)

你可以[按照文档](https://docs.semaphoreci.com/article/128-google-cloud-run-cicd)进行完整的演练。

## 本地应用程序设置

要运行微服务:

```
bundle install --path .bundle
bundle exec rackup 
```

要运行测试:

```
bundle exec rspec 
```

要构建和运行 Docker 容器:

```
docker build -t semaphore-demo-ruby-kubernetes
docker run -p 80:4567
```

…</article>

[View on GitHub](https://github.com/semaphoreci-demos/semaphore-demo-cloud-run)

### 启用云运行

Google 的官方[快速入门指南](https://cloud.google.com/run/docs/quickstarts/build-and-deploy)提供了开始使用 Cloud Run 的路线图。

第一步是:

*   [在您的帐户上启用云运行 API](http://console.cloud.google.com/apis/library/run.googleapis.com)；
*   安装谷歌云 SDK
*   用`gcloud components install beta`安装测试版组件，或者用`gcloud components update`更新它们。

### 整理您的申请

在我们的例子中，我们将使用一个简单的 Sinatra web 应用程序，它打包了以下 docker 文件:

```
FROM ruby:2.5

RUN apt-get update -qq && apt-get install -y build-essential

ENV APP_HOME /app
RUN mkdir $APP_HOME
WORKDIR $APP_HOME

ADD Gemfile* $APP_HOME/
RUN bundle install --without development test

ADD . $APP_HOME

EXPOSE 8080

CMD ["bundle", "exec", "rackup", "--host", "0.0.0.0", "-p", "8080"] 
```

Enter fullscreen mode Exit fullscreen mode

当修改您现有的 docker 文件时，确保应用程序在端口 8080 上运行可能是您需要做的唯一更改。如果不这样做，您可能会看到如下错误:

> 错误:(g cloud . beta run . deploy)容器启动失败。无法启动并监听由 port 环境变量定义的端口。此版本的日志可能包含更多信息。

### 向谷歌云和容器注册中心认证(GCR)

为了在 CI/CD 管道中自动将 Docker 图像推送到 GCR，Semaphore 需要通过 Google Cloud 认证。为了安全地做到这一点，我们需要[基于谷歌云服务账户的认证密钥创建一个信号量秘密](https://docs.semaphoreci.com/article/72-google-container-registry-gcr)。

一旦[获得了认证密钥](https://cloud.google.com/iam/docs/creating-managing-service-account-keys#iam-service-account-keys-list-gcloud)，使用信号量 CLI 将它作为秘密上传到信号量上。秘密应该定义一个文件，姑且称之为‌ `.secrets.gcp.json` :

```
$ sem create secret google-cloud-stg --file ~/Downloads/account-name-27f3a5bcea2d.json:.secrets.gcp.json 
```

Enter fullscreen mode Exit fullscreen mode

### 定义输送管道

我们现在可以编写一个信号量管道，它构建、标记并推送一个 Docker 容器到 GCR:

```
# .semaphore/docker-build.yml
# This pipeline runs after semaphore.yml
version: v1.0
name: Docker build
agent:
  machine:
    # Use a machine type with more RAM and CPU power for faster container
    # builds:
    type: e1-standard-4
    os_image: ubuntu1804
blocks:
  - name: Build
    task:
      # Mount a secret which defines an authentication key file.
      # For info on creating secrets, see:
      # - https://docs.semaphoreci.com/article/66-environment-variables-and-secrets
      # - https://docs.semaphoreci.com/article/72-google-container-registry-gcr
      secrets:
        - name: google-cloud-stg
      jobs:
      - name: Docker build
        commands:
          # Authenticate using the file injected from the secret
          - gcloud auth activate-service-account --key-file=.secrets.gcp.json
          # Configure access to container registry, silence confirmation prompts with -q
          - gcloud auth configure-docker -q

          - checkout

          # Tag your images with gcr.io/ACCOUNT_PROJECT_NAME/SERVICE_NAME pattern
          # Use Git SHA to produce unique artifacts
          - docker build -t "gcr.io/semaphore2-stg/semaphore-demo-cloud-run:${SEMAPHORE_GIT_SHA:0:7}" .
          - docker push "gcr.io/semaphore2-stg/semaphore-demo-cloud-run:${SEMAPHORE_GIT_SHA:0:7}"

promotions:
  # Deployment to staging can be trigger manually:
  - name: Deploy to staging
    pipeline_file: deploy-staging.yml

  # Automatically deploy to production on successful builds on master branch:
  - name: Deploy to production
    pipeline_file: deploy-production.yml
    auto_promote_on:
      - result: passed
        branch:
          - master 
```

Enter fullscreen mode Exit fullscreen mode

在`deploy-staging.yml`和`deploy-production.yml`中定义的管道运行相同的步骤，不同之处在于服务的名称。

下面是生产部署的运行方式:

```
# .semaphore/deploy-production.yml
# This pipeline runs after docker-build.yml
version: v1.0
name: Deploy to production
agent:
  machine:
    type: e1-standard-2
    os_image: ubuntu1804
blocks:
  - name: Deploy to production
    task:
      secrets:
        - name: google-cloud-stg
      jobs:
      - name: run deploy
        commands:
          - gcloud auth activate-service-account --key-file=.secrets.gcp.json
          - gcloud auth configure-docker -q

          # Deploy to Cloud Run, using flags to avoid interactive prompt
          # See https://cloud.google.com/sdk/gcloud/reference/beta/run/deploy
          - gcloud beta run deploy markoci-demo-cloud-run --project semaphore2-stg --image gcr.io/semaphore2-stg/markoci-demo-cloud-run:${SEMAPHORE_GIT_SHA:0:7} --region us-central1 
```

Enter fullscreen mode Exit fullscreen mode

## 即将上线

本地终端或信号量作业中部署日志的最后一行将包含新应用程序所在的 URL，例如`https://semaphore-demo-cloud-run-ud2bmvsmda-uc.a.run.app`。

当您第一次打开该 URL 时，您会看到:

> 错误:禁止
> 
> 您的客户端没有权限从此服务器获取 URL /

这是因为还有一个步骤要做，那就是[在 Google Cloud Run 控制台中公开](https://cloud.google.com/run/docs/securing/managing-access#making_a_service_public)你的服务。瞧:

[![web app running on google cloud run](img/2bfedf9014b26d12b0c9246a216b7e30.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3LonBqSx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fnwcfc5l365eh1y5iavz.png)

## 包装完毕

希望这篇文章已经启发了你，用 CI/CD 管道来构建和发布一些东西到 Google Cloud Run，以保证你的安全。下一步就看你的了。:)

资源:

*   [云运行快速入门指南](https://cloud.google.com/run/docs/quickstarts/build-and-deploy)
*   [GitHub 上的示例项目](https://github.com/semaphoreci-demos/semaphore-demo-cloud-run)