# 使用 Kubernetes & Container Builder 在 Google Cloud 中持续部署 NodeJS

> 原文：<https://dev.to/mfahlandt/nodejs-continuous-deployment-done-with-container-builder-and-kubernetes-engine-in-google-cloud-570p>

因此，您希望将您的应用程序部署到您的 Kubernetes 集群中，而不关心任何手动步骤？
我为您做好了准备，使用 Google Cloud 创建一个持续的部署管道非常简单。
为了便于理解，我选择了 NodeJS Express 应用程序，但它也可以与 react 或 PHP 或任何其他应用层一起工作。

让我们开始吧:

## 因为我是管理员

首先，我们需要给容器构建者访问我们的 Kubernetes API 的权利。请记住，这并不意味着可以访问某个集群。它只允许 cloudbuilder 服务帐户访问我们的 Kubernetes 集群。因此，请跳转到 [IAM 设置页面](https://console.cloud.google.com/iam-admin/iam?)并查找 cloudbuild 服务帐户。如果它不存在，你可能需要启用[云构建 API](https://console.cloud.google.com/cloud-build/)

应该是这样的
[![cloud build service account](img/05fdebc26341f39c3a11c078686599f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Anohwg-W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fuo41i04jht1xlcrof1n.png)

我们需要添加访问我们的集群的 Kubernetes API 的权限，所以单击笔，查看以下内容。

[![Kubernetes API access rights](img/a50441983c82d07f6ea750eadfbc6dc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0ewjHUNB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23xzi970t9u3gw2fammn.png)

## 准备申请

我不会详细介绍如何设置一个 express 应用程序并对其进行测试。
我用示例应用程序创建了一个存储库，我们可以使用它

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[mfahlandt](https://github.com/mfahlandt)/[GCP-连续部署-节点-演示](https://github.com/mfahlandt/gcp-continuous-deployment-node-demo)

### 这是一个示例项目，展示了如何轻松地创建到 google cloud 的持续部署

<article class="markdown-body entry-content container-lg" itemprop="text">

## NodeJS 使用容器构建器和 Kubernetes 引擎完成连续部署

要找到关于如何使用这个库的所有细节，请参考 [dev.to](https://dev.to) 上相应的 block 帖子

</article>

[View on GitHub](https://github.com/mfahlandt/gcp-continuous-deployment-node-demo)

为了给你一个概述，我们有一个基本的快速应用程序与 2 后端路线检索用户或用户的 id。我们还有一个测试文件夹，里面有两条路线的测试。这些测试是在柴和摩卡的帮助下编写的。
如果您下载了存储库，您可以执行以下操作来查看测试是否正常工作。

```
 npm install
npm test 
```

Enter fullscreen mode Exit fullscreen mode

在应用程序运行之前，我们需要在 Kubernetes 集群中进行服务和部署。因此，让我们快速创建一个服务和一个部署。所有的文件都可以在存储库中找到。

```
 apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: server-production
  labels:
    app: YOUR-PROJECT-ID
spec:
  replicas: 2
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  template:
    metadata:
      labels:
        app: server
    spec:
      containers:
        - name: server
          image: gcr.io/PROJECT_ID/REPOSITORY:master
          imagePullPolicy: Always
          ports:
            - containerPort: 3000
          env:
            - name: NODE_ENV
              value: "production" 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一重要的部分是您将项目 id 和存储库更改为存储库将拥有的路径。

在这之后，我们只需要一个服务来将我们的应用程序暴露在互联网上。所以赶紧申请服务吧。

```
 kind: Service
apiVersion: v1
metadata:
  name:  server
spec:
  selector:
    app:  server
  ports:
    - name:  server
      protocol: TCP
      port: 80
      targetPort: 3000
  type: LoadBalancer 
```

Enter fullscreen mode Exit fullscreen mode

## 准备部署

现在我们需要进入整个设置中最重要的部分。在那里，我们将为我们的持续部署步骤定义一切。

第一个令人惊奇的部分是，可以将所有重要的数据放在构建中定义的环境变量中，因此您可以将云构建用于不同的设置。

首先，我们安装所有的节点依赖项并运行测试。

```
 - name: 'gcr.io/cloud-builders/npm'
    args: ['install']
  - name: 'gcr.io/cloud-builders/npm'
    args: ['run', 'test'] 
```

Enter fullscreen mode Exit fullscreen mode

在此之后，我们构建一个 docker 映像，其中包含所有存储库文件和一个适当定义的环境，因此您也可以轻松地进行分段部署，甚至是分支部署。我们把它推送到谷歌图像库。

```
 - name: 'gcr.io/cloud-builders/docker'
    args:
      - build
      - '--build-arg'
      - 'buildtime_variable=$_NODE_ENV'
      - '-t'
      - gcr.io/$PROJECT_ID/$REPO_NAME:$BUILD_ID
      - '.'
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'gcr.io/$PROJECT_ID/$REPO_NAME:$BUILD_ID'] 
```

Enter fullscreen mode Exit fullscreen mode

同样重要的是，我们用惟一的构建 id 标记图像，以利用 kubernetes 的应用能力，因此图像实际上被改变了。

```
 - name: 'gcr.io/cloud-builders/kubectl'
    args:
      - set
      - image
      - deployment
      - $_DEPLOYMENT
      - $_DEPLOYMENT=gcr.io/$PROJECT_ID/$REPO_NAME:$BUILD_ID
    env:
      - 'CLOUDSDK_COMPUTE_ZONE=$_CLUSTER_ZONE'
      - 'CLOUDSDK_CONTAINER_CLUSTER=$_CLUSTER_NAME' 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将图像设置在 kubernetes 集群中。嘭！提交钩子，自动化测试，如果成功的自动化部署，没有停机时间。

现在我们打开[容器构建器触发器](https://console.cloud.google.com/cloud-build/triggers/add)并选择我们的代码所在的位置。

[![create trigger](img/c6a9fab918f10d8e227527d8b663fb6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xM6eHyjw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k21b5ghqguxa5lyk59zq.png)

在最后一个触发步骤中，我们现在可以添加自定义变量。这是我们实际定义集群的第一点。所以所有东西都聚集在一个地方，随时可以使用。

[![create trigger part 2](img/78beb3cb2d1ff4b758a0f9251c1905be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j8DuOH0M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n3avd0br94aosba1x20z.png)

现在我们只需要向主服务器提交，触发器就启动了。

[![Build successful](img/ddb923f9a61bb5bc6d790b861478c996.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JW_F-9FM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/17nn3i98r2eym3mmjyy6.png)

YIHA:现在我们有了持续的部署，无需设置任何额外的服务，如 jenkins、ant 或 chef。相当惊人

我想在云中创建一个从零到英雄的教程系列，你有兴趣吗？给我留言吧！