# Firebase 多站点托管

> 原文：<https://dev.to/codingcatdev/firebase-multisite-hosting-66e>

> 原帖:[https://ajonp.com/lessons/firebase-multisite-hosting/](https://ajonp.com/lessons/firebase-multisite-hosting/)

[https://www.youtube.com/embed/bLrZxoC0VlQ](https://www.youtube.com/embed/bLrZxoC0VlQ)

# Firebase 多站点托管(多个站点)

这一课将重点介绍一个非常简单的解决方案，让多个 web 项目托管在同一个域下。

> Firebase 为您的所有项目限定了一个域。一旦您关联了此域，您就不能在任何其他项目中再次使用它。例如，我们想使用 lessons.ajonp.com，但由于我们使用 ajonp-ajonp-com 的 ajonp.com 域，我们无法使用这一点。
> 
> 我们还想提一下，视频可能不完全符合第 7 课回购中的内容，我们对此感到抱歉，最初我们打算使用一个域的两个项目。请直接提取回购，这些步骤应该可以帮助您快速完成设置。

## 上课步骤

1.  切换火力基地计划
2.  创建 4 个 Firebase 托管站点
3.  部署到 Firebase Multisite 的多个 Hugo 主题示例
4.  多个项目部署到 Firebase Multisite 的 Angular 项目示例
5.  谷歌云构建器-触发器

# Firebase 定价计划

## 新建项目

如果您刚刚创建了一个新项目，您需要升级才能使用多站点托管。

[![Upgrade Plan](img/c32b3a6917dc9a1b98e7685a1ef7b987.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UMDNrDAV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1545142662/ajonp-ajonp-com/7-lesson-firebase-multisite-hosting/Screen_Shot_2018-12-17_at_2.56.02_PM.jpg)

[![Included Pricing](img/331de8acda1c9c99ab787778dd7ff2a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UTGD6A_r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1545142662/ajonp-ajonp-com/7-lesson-firebase-multisite-hosting/Screen_Shot_2018-12-17_at_3.00.55_PM.jpg)

我们推荐 Blaze 计划，因为你最终会为你所有的小项目少付很多钱(如果有的话)。如果你参加火焰计划，你保证支付 25 美元。

[![Firebase Plans](img/5b2b750ad35406f32af8edb7703a92cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d8YUDy7T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1545142662/ajonp-ajonp-com/7-lesson-firebase-multisite-hosting/Screen_Shot_2018-12-17_at_2.57.48_PM.jpg)

火舞计划
[![Free Included Items](img/0f0e6b63c2c62fbfb6b7decfb496e9fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hv7wynaE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1545142662/ajonp-ajonp-com/7-lesson-firebase-multisite-hosting/Screen_Shot_2018-12-17_at_3.01.55_PM.jpg) 上的托管免费限额

# 创建 4 个 Firebase 托管站点

你可以给它们起任何适合你的项目的名字，如果你使用 ajonp*你可能会有问题，因为我们已经在用了。

*   [ajonp-第七课(雨果)](https://ajonp-lesson-7.firebaseapp.com/)
*   [ajonp-lesson-7-admin(Angular)](https://ajonp-lesson-7-admin.firebaseapp.com)

*   [ajonp-lesson-7-app(Angular)](https://ajonp-lesson-7-app.firebaseapp.com)

您的站点示例:

*   mysite
*   mysite 管理员
*   mysite-amp
*   mysite 应用程序

[![Example Site Creation](img/6cfa695cc1949da417c8891b4ca35e52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N3aFonfE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1545158121/ajonp-ajonp-com/7-lesson-firebase-multisite-hosting/axpst0mag0khcvqqlijc.jpg)
[![All Sites](img/17b3c86083d4b441a0ebc82cb0e4c387.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--ahDZoX1H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1545160404/ajonp-ajonp-com/7-lesson-firebase-multisite-hosting/aaqgcyvubuggrk8fsqqm.jpg)

# 部署到 Firebase Multisite 的多个 Hugo 主题示例

> 如果你只是部署到谷歌云，你不需要安装任何东西。在本课中，参考[云构建](https://ajonp.com/lessons/7-firebase-multisite-hosting/#cloud-build)另请参见 https://ajonp.com/lessons/2-firebase-ci/了解有关 CI/CD 的更多详细信息。

一旦你克隆了 https://github . com/AJONPLLC/lesson-7-firebase-multisite-hosting，你就可以在本地运行它，但是在此之前你必须构建所有的站点。

## 删除 git 引用

请删除对 AJONPLLC 的引用，然后您可以添加任何您想要的 git repo。

```
git remote rm origin 
```

Enter fullscreen mode Exit fullscreen mode

## 添加新的引用

1.  创建一个新的 github 站点(或 Gitlab 或 Google Cloud 或...)
    [![Git Sample Repo](img/0965384e2d298b580087ca99df18819d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--YmKGkiXM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1545162597/ajonp-ajonp-com/7-lesson-firebase-multisite-hosting/lnstebijo3p2ijyn1jzw.jpg)

2.  然后使用这个命令将新的 repo 添加回您的站点，用您的命令替换 **ajonp/a-sample-repo.git** 。

```
git remote add origin https://github.com/ajonp/a-sample-repo.git
git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

## 雨果遗址建设步骤

1.  Git 子模块命令，确保你有 [Git CLI](https://git-scm.com/downloads) 。

```
git submodule init
git submodule update --recursive --remote 
```

Enter fullscreen mode Exit fullscreen mode

这将更新到 ajonp.com 的最新远程版本，包括我们公开托管的内容和主题

1.  Hugo 命令，确保你已经安装了 [Hugo CLI](https://gohugo.io/getting-started/installing/) 。

```
hugo -d dist/ajonp -v -t ajonp-hugo-ionic --config config.toml,production-config.toml  
hugo -d dist/ajonp-amp -v -t ajonp-hugo-amp --config config.toml,production-config.toml 
```

Enter fullscreen mode Exit fullscreen mode

# 多个项目部署到 Firebase Multisite 的 Angular 项目示例

## 建角点的步骤

1.  NPM 命令，确保你已经安装了节点 Js。

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

1.  角度命令，确保安装了[角度命令](https://cli.angular.io/)。

```
ng build --prod --project ajonp-admin
ng build --prod --project ajonp-app 
```

Enter fullscreen mode Exit fullscreen mode

1.  使用 firebase 本地服务，确保您有 [Firebase CLI](https://firebase.google.com/docs/cli/)

```
firebase serve 
```

Enter fullscreen mode Exit fullscreen mode

# 谷歌云构建器-触发器

请向您的存储库参考添加触发器 [Firebase CI/CD 课程](https://ajonp.com/lessons/2-firebase-ci/)以了解更多详细信息。

## 云搭

现在，在尝试了所有的手动构建步骤之后，您真的不需要它们了。通过从上面设置提交触发器，您可以在云中运行所有这些。

cloudbuild.yaml

```
steps:
# Pull in the required submodules for our themes and content
- name: gcr.io/cloud-builders/git
  args: ['submodule', 'init']
- name: gcr.io/cloud-builders/git
  args: ['submodule', 'update', '--recursive', '--remote']
# Install all of our dependencies
- name: 'gcr.io/cloud-builders/npm'
  args: ['install']
# Build the hugo image
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/hugo', './dockerfiles/hugo' ]
# Build ajonp-hugo-ionic -> dist/ajonp
- name: 'gcr.io/$PROJECT_ID/hugo'
  args: [ 'hugo',"-d", "dist/ajonp", "-v", "-t", "ajonp-hugo-ionic", "--config", "config.toml,production-config.toml"]
# Build ajonp-hugo-amp -> dist/ajonp-amp
- name: 'gcr.io/$PROJECT_ID/hugo'
  args: [ 'hugo',"-d", "dist/ajonp-amp", "-v", "-t", "ajonp-hugo-amp", "--config", "config.toml,production-config.toml"]
# Build the hugo image
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/ng:latest', './dockerfiles/ng' ]
# Build ajonp-admin -> dist/ajonp-admin
- name: 'gcr.io/$PROJECT_ID/ng:latest'
  args: ['build', '--prod', '--project', 'ajonp-admin']
# Build ajonp-admin -> dist/ajonp-app
- name: 'gcr.io/$PROJECT_ID/ng:latest'
  args: ['build', '--prod', '--project', 'ajonp-app']
# Build the firebase image
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/firebase', './dockerfiles/firebase' ]
# Deploy to firebase
- name: 'gcr.io/$PROJECT_ID/firebase'
  args: ['deploy', '--token', '${_FIREBASE_TOKEN}']
# Optionally you can keep the build images
# images: ['gcr.io/$PROJECT_ID/hugo', 'gcr.io/$PROJECT_ID/firebase'] 
```

Enter fullscreen mode Exit fullscreen mode

## 本地云构建

如果您想本地调试，请确保您阅读了[本地构建和调试](https://cloud.google.com/cloud-build/docs/build-debug-locally)。
这需要你在本地安装 [Docker](https://docs.docker.com/install/#server) 。

### 安装必要的部件

安装 docker-credential-gcr

```
gcloud components install docker-credential-gcr 
```

Enter fullscreen mode Exit fullscreen mode

配置 Docker

```
docker-credential-gcr configure-docker 
```

Enter fullscreen mode Exit fullscreen mode

检查版本是否正常工作

```
cloud-build-local --version 
```

Enter fullscreen mode Exit fullscreen mode

### 本地构建

在你的项目的根目录下，你可以运行这个命令(**不要忘记**. "因为这告诉 Cloudbuild 使用当前目录)

```
cloud-build-local --config=cloudbuild.yaml --dryrun=false --substitutions _FIREBASE_TOKEN=EXAMPLE . 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到，当 Google Cloud Build 执行时，这个输出与云中发生的情况相匹配

本地输出

```
Starting Step #0
Step #0: Pulling image: gcr.io/cloud-builders/git
Step #0: Unable to find image 'gcr.io/cloud-builders/docker:latest' locally
Step #0: latest: Pulling from cloud-builders/docker
Step #0: 75f546e73d8b: Pulling fs layer
Step #0: 0f3bb76fc390: Pulling fs layer
Step #0: 3c2cba919283: Pulling fs layer
Step #0: 252104ea43c6: Pulling fs layer
Step #0: 252104ea43c6: Waiting
Step #0: 3c2cba919283: Verifying Checksum
Step #0: 3c2cba919283: Download complete
Step #0: 0f3bb76fc390: Verifying Checksum
Step #0: 0f3bb76fc390: Download complete
Step #0: 75f546e73d8b: Verifying Checksum
Step #0: 75f546e73d8b: Download complete
Step #0: 75f546e73d8b: Pull complete
Step #0: 0f3bb76fc390: Pull complete
Step #0: 3c2cba919283: Pull complete 
```

Enter fullscreen mode Exit fullscreen mode

Google Cloud Builder 输出
[![Google Cloud Builder Output ](img/2dae1230aaeb0e2e3d8efad42b6a2bca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K0MUzliq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1545169224/ajonp-ajonp-com/7-lesson-firebase-multisite-hosting/ln3docmhljhbkphqio8a.jpg)