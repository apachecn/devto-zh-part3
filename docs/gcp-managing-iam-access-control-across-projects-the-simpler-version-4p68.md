# GCP:管理跨项目的 IAM 访问控制-更简单的版本

> 原文：<https://dev.to/tonymet/gcp-managing-iam-access-control-across-projects-the-simpler-version-4p68>

GCP 资源被组织到项目中——所有资源 ID 和 IAMprinciples 被分组到一个项目 ID 下。这意味着默认情况下，分配给委托人的角色(例如，用户或服务帐户)的范围仅限于项目资源。如果你的图像在一个项目的存储桶中，而你的应用程序在另一个项目中运行，这可能会很棘手

如果您想在一个项目中提供一个服务原则来访问另一个项目中的资源，这种方法并不明显，也没有很好的文档记录。

下面我们将讨论最直接的方法，它适用于不属于组织的项目。这对于由不同团队管理的项目或者作为个人账户一部分的 GCP 项目(没有 gsuite)来说是很好的

## 用例-允许外部应用引擎应用访问构建状态

对于这个例子，在`${SOURCE_PROJECT_ID}`中运行的 app engine 应用程序需要访问`${GOOGLE_CLOUD_PROJECT}`中的构建状态。

这可以使用为项目 id = `${GOOGLE_CLOUD_PROJECT}`激活的云外壳轻松完成

### 授予外部服务原则一个角色

```
gcloud projects add-iam-policy-binding $GOOGLE\_CLOUD\_PROJECT \--member=serviceAccount:${SOURCE\_PROJECT\_ID}@appspot.gserviceaccount.com \--role=roles/cloudbuild.builds.viewer \ 
```

## 这是怎么回事？

项目的 GCP 访问控制是一个 ACL 映射成员->角色。上述命令直接引用外部帐户中的 app engine 服务帐户(`PROJECT_ID@appspot.gserviceaccount.com`)，并附加`cloudbuild.builds.viewer`角色

例如，您可以这样查看整个项目级 ACL

```
gcloud projects get-iam-policy `${GOOGLE\_CLOUD\_PROJECT}`bindings:- members: - serviceAccount:XXXXX@cloudbuild.gserviceaccount.com - serviceAccount:local-dev@XXXXXX.iam.gserviceaccount.com role: roles/appengine.appAdmin- members: - serviceAccount:XXXXXX@cloudbuild.gserviceaccount.com 
```

## 这样不会乱吗？

任何一个理智的人都可以看出这并不合适。如果依赖往往是单向的，这可能会奏效，但一旦你获得双向依赖，那将是一场灾难。此时，查看[创建一个组织并在组织级别管理 ACL](https://cloud.google.com/resource-manager/docs/access-control-org)

## 这个怎么比 AWS IAM 好或者差？

基于项目的资源配置有利有弊。有利的一面是，项目的工作方式类似于 c++命名空间，因此所有资源都被隐式地授予同一个项目中的主体。AWS 策略包含一个显式的 resourcepattern 匹配，这使得每个授权更加复杂。

默认情况下安全性也得到了提高，因为 deny 是项目中的默认设置。

## 比较大的项目怎么样？

**TL；博士**，不要把你所有的资源放在一个项目上，当你的项目开始有双向 dep 时，考虑把它们转移到一个星形模式中，并使用一个 GCP 组织作为 ACL