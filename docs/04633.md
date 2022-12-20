# 用 Firebase、Hugo 和 Google Cloud Builder 发布免费静态网站-第 2 部分

> 原文：<https://dev.to/tonymet/publish-free-static-websites-with-firebase-hugo-and-google-cloud-builder-part-2-45cc>

在第 1 部分中，我们完成了
我们的开发环境，包括设置 Hugo 和我们的 repo。

在这里，我们将我们的站点发布到 Firebase Hosting，并在
[Google Cloud Build](https://cloud.google.com/cloud-build/) 上创建 CI 工具，以在推送时进行构建和发布。

## 打开你的云壳

在第 1 部分中，我们用 hugo 增强了我们的
云外壳，并建立了我们的回购协议。在第二部分中，我们将使用它来
创建构建器和配置主机。

[参见快速入门了解完整说明](https://cloud.google.com/shell/docs/quickstart)

## 创建一个 Firebase 站点&配置你的项目

首先，验证您的 cli

```
# --no-localhost let's us authenticate our cloud shell by code
$ firebase login --no-localhost 
```

使用 [firebase 控制台](https://console.firebase.google.com)创建一个站点

在 hugo 项目中运行 firebase init，选择`hosting`和您的站点名称

```
firebase init 
```

您的首次部署

```
# build the site
$ hugo
# deploy
$ firebase deploy
➜  quickstart git:(master) ✗ firebase deploy

=== Deploying to 'xxxx'...

i  deploying hosting
i  hosting[xxxx]: beginning deploy...
i  hosting[xxxx]: found 15 files in public 
```

## 创建云构建工作流

[Google Cloud Build](https://cloud.google.com/cloud-build/) 采用了独特的
设计方法，避免了传统的 shell-script 接口用于
定义构建步骤。

我们来分解一个例子`cloudbuild.yaml` :

```
steps:
- name: gcr.io/$PROJECT_ID/hugo
- name: gcr.io/$PROJECT_ID/firebase
  args: ['deploy'] 
```

在这种情况下，每个步骤都被定义为一个唯一的 docker 映像(除了少数例外，您将它放在您的 GCP 项目中)，以及这些步骤的参数。

因此，引用的任何步骤都必须首先构建并推送到项目的
Docker 注册表中。为了让这变得简单，用户贡献的步骤可以在
[云构建者社区
回购](https://github.com/GoogleCloudPlatform/cloud-builders-community)中找到

甚至像`mkdir`这样基本的东西也必须定义为 docker 图像

每个步骤共享一个`/workspace`目录，允许每个步骤将结果传递给下一个步骤。

### 建造雨果&建造火垒的步骤

```
$ git clone git@github.com:GoogleCloudPlatform/cloud-builders-community.git; cd cloud-builders-community
$ pushd hugo     && gcloud builds submit . && popd
$ pushd firebase && gcloud builds submit . && popd 
```

### 为 Hugo 配置云构建，提交构建

在`hugo-sample-project`目录内，添加`cloudbuild.yaml`

```
steps:
- name: gcr.io/$PROJECT_ID/hugo
- name: gcr.io/$PROJECT_ID/firebase
  args: ['deploy'] 
```

并提交一个构建版本

```
$ gcloud builds submit . 
```

Gcloud 会推送你的 hugo 源文件和`cloudbuild.yaml`来触发你在 GCP 上的
构建

## 测试和总结

此时，访问您的 firebase 站点 URL(它将显示在 cloudbuild 日志中)——您应该看到您的站点已经部署好了。

通过我们所做的这些工作:

*   一个可扩展的静态网站——可以是一个反应温泉或个人博客
*   云托管的开发环境，全球可访问且始终在线
*   允许多个开发人员测试和部署变更的自动化 CI 平台