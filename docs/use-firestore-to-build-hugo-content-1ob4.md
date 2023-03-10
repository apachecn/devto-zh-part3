# 使用 firestorm 构建 hugo 内容

> 原文：<https://dev.to/codingcatdev/use-firestore-to-build-hugo-content-1ob4>

原帖:[https://ajonp . com/lessons/use-firestore-to-build-Hugo-content/](https://ajonp.com/lessons/use-firestore-to-build-hugo-content/)

[https://www.youtube.com/embed/rMcpwucRlnU](https://www.youtube.com/embed/rMcpwucRlnU)

# 凡士多给雨果

本课将探索一些相当复杂的触发器和 Git 实践。

🌎演示:
[角管理站点](https://ajonp-lesson-8-admin.firebaseapp.com)
[雨果主站点](https://ajonp-lesson-8-hugo.firebaseapp.com/)

## 上课步骤

1.  [计费限额提醒](#billing-limit-reminder)
2.  [叉/克隆第八课——雨果](#fork-clone-lesson-8-hugo)
3.  [创建 Hugo firebase 托管网站](#create-hugo-firebase-hosting-site)
4.  [创建 Hugo 内容谷歌云构建触发器](#create-hugo-content-google-cloud-build-trigger)
5.  [测试云构建触发器](#test-cloud-build-trigger)
6.  [分叉/克隆第八课-firestore-功能](#fork-clone-lesson-8-firestore-functions)
7.  [建角工程](#build-angular-project)
8.  [从 CLI 部署 Firebase 主机](#deploy-firebase-hosting-from-cli)
9.  [更新 Firestore 触发器以匹配您的 GitHub repo](#update-firestore-trigger-to-match-your-github-repo)
10.  [从 CLI 部署 Firebase 功能](#deploy-firebase-functions-from-cli)
11.  [添加一本书，观看它在 Hugo 网站的展示](#add-a-book-watch-it-show-in-hugo-site)

可选择的

*   针对 CI/CD 的云构建

# 计费限额提醒

> 提醒你尽量保持自由。你不应该遇到问题，但以防万一。在你的[账单](https://console.cloud.google.com/billing/)面板中，为你的项目创建一个新的预算，以防你尝试这个超过 50 次。

如果你想给构建设置一个硬的上限，签出[封顶 API 使用](https://cloud.google.com/apis/docs/capping-api-usage)。

对于构建 API，您可以转到[云构建 API 配额](https://console.cloud.google.com/apis/api/cloudbuild.googleapis.com/quotas)

更新结帐 [IAM & Admin- >配额](https://console.cloud.google.com/iam-admin/quotas)

# 叉子/克隆人第八课-雨果

你可以克隆第八课——Hugo——T1，但是如果你想为谷歌云的构建制造任何触发器，我会建议你使用自己的回购。

## 克隆你的叉子

我将通过分叉到我的 ajonp 帐户来运行我们的示例，您应该看到它“分叉自 AJONPLLC/lesson-8-hugo”

[![Fork Example](img/30568b5a553484cb974bfddc70c187a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Mz03xPN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545864186/ajonp-ajonp-com/8-lesson-firestore-functions/qvn7dthjxnhelvyg20rv.jpg)

您可以通过在该命令中替换您的 _name 来实现这一点。

```
git clone https://github.com/<your_name>/lesson-8-hugo.git && cd lesson-8-hugo 
```

Enter fullscreen mode Exit fullscreen mode

## 测试本地服务 hugo

你的主题文件夹将是空的，因为它引用了一个 git 子模块。首先确保本地克隆了所有子模块。

```
git submodule init && git submodule update --remote 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们运行 hugo serve 命令。这告诉 hugo 使用 ajonp-hugo-ionic 主题提供服务，并使用 config.toml 文件。

```
hugo server -t ajonp-hugo-ionic --config config.toml 
```

Enter fullscreen mode Exit fullscreen mode

您现在应该会看到主页上列出了`http://localhost:1313/`的最新书籍。

[![Latest Books](img/7585a27820b7ed4f2eb0f83a8d13d773.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mtMOFvH---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545864982/ajonp-ajonp-com/8-lesson-firestore-functions/dcrwjvqsbycdqflpo4nj.jpg)

还可以在`http://localhost:1313/books/`以列表格式查看所有书籍。

[![All Books](img/038822e786f977458b5bc72dfb58616b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wAjKZCWQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545865091/ajonp-ajonp-com/8-lesson-firestore-functions/g6mu7mmskbfdb2pdpvdv.jpg)

这将基于`AJONPLLC/lesson-8-hugo`中的最新内容，如果您不想要任何内容，请随意清除`content/books`中的所有文件。

> 记住 Hugo 是动态构建文件的，所以如果你删除了`content/books`中的所有文件，你会在`http://localhost:1313/books/`中得到一个 404。

# 创建 Hugo firebase 托管站点

## 创建 firebase 项目

从添加新项目开始

[![Add Project](img/c128fa54d6dfb898089964ca013b930e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Msb98O-p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545865510/ajonp-ajonp-com/8-lesson-firestore-functions/mlcfmsxq3egli5llns6i.jpg)

给项目起个好名字。

[![Name Project](img/2a246148f12df0e031de66f4ea559041.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xnh5s9Lg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545866565/ajonp-ajonp-com/8-lesson-firestore-functions/s7cmb6rmkkft4v0ag7ps.jpg)

然后，您可以按照主机下的入门，或按照更新本地 firebase 文件。[![](img/e0c872b58e0c5078cc668e50af599865.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZFg3l_YO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545866746/ajonp-ajonp-com/8-lesson-firestore-functions/ragqsnmidwfuv1kcb51n.jpg)

## 更新本地 firebase 文件

有几个 firebase 命令可以用来手动添加正确的主机设置`firebase use ajonp-lesson-8`和`firebase target:apply hosting ajonp-lesson-8-hugo ajonp-lesson-8-hugo`。但是，我觉得只更新两个文件更容易。

.firebaserc

```
{  "projects":  {  "default":  "ajonp-lesson-8"  },  "targets":  {  "ajonp-lesson-8":  {  "hosting":  {  "ajonp-lesson-8-hugo":  [  "ajonp-lesson-8-hugo"  ]  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

您的更新
。firebaserc

```
{  "projects":  {  "default":  "your-project"  },  "targets":  {  "your-project":  {  "hosting":  {  "your-hosting-name":  [  "your-hosting-name"  ]  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例中，替换默认项目以匹配您的项目。对于目标，这是将您的项目映射到您的主机名称，我们将 firebase.json 中的主机名称定义为目标。

firebase.json

```
{  "hosting":  {  "target":  "ajonp-lesson-8-hugo",  "public":  "public",  "ignore":  [  "firebase.json",  "**/.*",  "**/node_modules/**"  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

您的更新
firebase.json

```
{  "hosting":  {  "target":  "your-hosting-name",  "public":  "public",  "ignore":  [  "firebase.json",  "**/.*",  "**/node_modules/**"  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，您将能够使用 firebase CLI 部署您的站点。

```
firebase deploy 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到一个包含大约 53 个文件的结果。

> 如果你只看到几个检查，你运行子模块更新。

[![firebase deploy result](img/f9dfa0d24fa85a754ec6b96d1c0c3ea6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y4PJz_yj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545874713/ajonp-ajonp-com/8-lesson-firestore-functions/nwsqimwac9suyzhrtdje.jpg)

# 创建 Hugo 内容谷歌云构建触发器

既然我们已经手动测试了一切正常，我们想要验证我们可以在每次提交时触发 Hugo 构建。

## 谷歌云项目

每个 Firebase 项目实际上只是一个谷歌平台项目。为了使用云构建器，我们需要启用计费，我建议切换到 Blaze 计划，因为如果您保持在限额内，您将无需支付任何费用。
[![Blaze Plan](img/6e8ab832b2d78a47a656ad5698a5ff74.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Otmwl50F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1545875585/ajonp-ajonp-com/8-lesson-firestore-functions/rsuwpz0erldnoexzhowl.jpg)

现在开始进入[谷歌云控制台](https://console.cloud.google.com)。

您应该看到项目的下拉选择，选择正确的项目。
[![Project Dropdown](img/e5a94c8e2534af7d93f72030ec696733.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--OS6gL5UO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545875207/ajonp-ajonp-com/8-lesson-firestore-functions/ufwyzjjozoxruj7zwqpi.jpg)

### 谷歌云触发启用

现在，您可以导航到[产品- >工具- >云构建- >触发器](https://console.cloud.google.com/cloud-build/triggers)

[![Cloud Trigger](img/b0d75f110d43e05880f2474b031cd522.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JZZkzA_V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545875313/ajonp-ajonp-com/8-lesson-firestore-functions/somhpisvlmnmitqf8apr.jpg)

如果这是一个新项目，您将需要启用云构建 API。

[![Cloud Build Enable API](img/d1a7bf6693437be09ef98bfcfab5f9c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3t_fRUKX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545875377/ajonp-ajonp-com/8-lesson-firestore-functions/mkfsasxbko8bx70oahfy.jpg)

> 如果您收到一个错误，请确保您已经在 Firebase 中更改为火焰计划。[![Builder Error](img/018e911aa60e4d94f3b9232db037a2c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dbbJKmcA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545875470/ajonp-ajonp-com/8-lesson-firestore-functions/jgp7vmvazhyi4c5p2y64.jpg)

### 谷歌云触发创建

如果你在本课中需要更多帮助，请查看我们的[Google Cloud Repositories CI/CD](https://ajonp.com/lessons/2-firebase-ci/)以获得完整的演示。

添加新触发器

从 GitHub 选择来源，并同意。

[![Select Source](img/07c74b7c4f0a8aa6b115efa868627ab5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NATJYbOj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545876414/ajonp-ajonp-com/8-lesson-firestore-functions/zc1t0zguz0xnccruayzq.jpg)

在您通过 GitHub 认证后，选择您的项目并继续。

[![Choose GitHub project](img/d1fb4724793f2c4a6d9d951ea4a5cec0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h4gIQi24--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545876452/ajonp-ajonp-com/8-lesson-firestore-functions/knehapxggrolfxkpaeor.jpg)

设置

*   名称:雨果 CI/CD
*   分支:主
*   构建配置:cloudbuild.yaml
*   替代变量:_FIREBASE_TOKEN = yourtoken。![Final steps](img/5865163351c46eed3142cc1b51a98ad3.png)

# 测试云构建触发器

在继续之前，我们想确定你的触发器正在工作。因此，让我们将一个样本文件加载到`content/books`文件夹中。将 examplebook.md 重命名为 testtrigger.md，或者创建一个新文件。

content/books/test trigger . MD

```
+++
title = "Example Book"
date = 2018-11-26T14:45:13-05:00
images = ["https://res.cloudinary.com/ajonp/image/upload/w_500,f_auto,fl_lossy,q_auto/v1545282630/ajonp-ajonp-com/8-lesson-firestore-functions/bookExample.jpg"]
+++

This is a commit test creating a book.😸 
```

Enter fullscreen mode Exit fullscreen mode

## 本地 git 推送

如果需要，添加新文件。

```
git add . 
```

Enter fullscreen mode Exit fullscreen mode

本地提交

```
git commit -m "Trigger Commit" 
```

Enter fullscreen mode Exit fullscreen mode

然后推送到远程 GitHub 存储库。

```
git push origin 
```

Enter fullscreen mode Exit fullscreen mode

## 触发历史

现在，您应该可以在 [Google Cloud 构建历史](https://console.cloud.google.com/cloud-build/builds)
[![Trigger History](img/3f4ae1a28be8477a3b1415fe022cf245.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--POc9fGZt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545877459/ajonp-ajonp-com/8-lesson-firestore-functions/llkuo8erhprsak23frtv.jpg) 中看到这个触发器的历史

## 自动部署到 Firebase

cloudbuild.yaml 中的最后一步是部署到 firebase，您应该会在云构建日志中看到一条成功部署的消息。

```
Starting Step #5
Step #5: Already have image: gcr.io/ajonp-lesson-8/firebase
Step #5: 
Step #5: === Deploying to 'ajonp-lesson-8'...
Step #5: 
Step #5: i  deploying hosting
Step #5: i  hosting[ajonp-lesson-8]: beginning deploy...
Step #5: i  hosting[ajonp-lesson-8]: found 54 files in public
Step #5: i  hosting: uploading new files [2/48] (4%)
Step #5: ✔  hosting[ajonp-lesson-8]: file upload complete
Step #5: i  hosting[ajonp-lesson-8]: finalizing version...
Step #5: ✔  hosting[ajonp-lesson-8]: version finalized
Step #5: i  hosting[ajonp-lesson-8]: releasing new version...
Step #5: ✔  hosting[ajonp-lesson-8]: release complete
Step #5: 
Step #5: ✔  Deploy complete!
Step #5: 
Step #5: Project Console: https://console.firebase.google.com/project/ajonp-lesson-8/overview
Step #5: Hosting URL: https://ajonp-lesson-8.firebaseapp.com
Finished Step #5 
```

Enter fullscreen mode Exit fullscreen mode

> 提醒如果您看不到新文件，它可能缓存在浏览器/服务人员中，您可以强制刷新浏览器来查看。

# 分叉/克隆第八课-firestore-功能

第八课-firestore-functions 设置为网站的管理端，将与 [Firebase Firestore](https://firebase.google.com/docs/firestore/) 交互。我是用 Angular 写的，但是你可以使用任何网络框架，iOS，Android，Unity...

## 安装 npm 依赖项

验证您是否位于基本目录

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

## 就地服务

此时，您仍然指向 AJONPLLC 项目数据库，因此您会看到列出了一些书籍。您必须切换到我们在上面创建的新 firebase 项目。

## 更新 firebase 配置

> 您只需要一个环境文件，但是我经常同时拥有开发和生产设置。

在您的 Firebase 项目概述中，有一个设置齿轮->项目设置。
[![Project Settings](img/dc2e9133116ba8e89f2c8e898df951ed.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--zmDicVGA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545921872/ajonp-ajonp-com/8-lesson-firestore-functions/msoukza2h7qt3caep5op.jpg)

然后在“你的应用程序”部分选择“将 Firebase 添加到你的 web 应用程序”。
[![Your apps](img/604e84330a0cfa9833668b71b2be9995.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z_FwKUgu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545921940/ajonp-ajonp-com/8-lesson-firestore-functions/dt6rddue4ra5oezt4fmi.jpg)

复制分配给 config 的 Javascript 对象，下一步我们将把它粘贴到我们的环境文件中。

```
{
    apiKey: "your-apiKey",
    authDomain: "your-project.firebaseapp.com",
    databaseURL: "https://your-project.firebaseio.com",
    projectId: "your-project",
    storageBucket: "your-project.appspot.com",
    messagingSenderId: "your-messagingSenderId"
} 
```

Enter fullscreen mode Exit fullscreen mode

src/environments/environment . ts 和 src/environment/environment . prod . ts

[![environment files](img/7f34384046c4c0c5b2e67932ac7568e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5nyTT7ni--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545921794/ajonp-ajonp-com/8-lesson-firestore-functions/k4oljh0egz3ajrt9reo8.jpg)

## 认证

现在，因为这是一个新项目，我们正在引导这个应用程序，如果您返回到`http://localhost:4200`，您很可能会看到一个错误，因为我们尚未更新我们的身份验证设置。

[![Auth Error](img/cead3e0a651f802e94fbfcb345f83c09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rqi_iJPC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1545922204/ajonp-ajonp-com/8-lesson-firestore-functions/tofhpbinmrcwi8c7kzyt.jpg)

您可以在身份验证->登录方法中更新此信息。编辑 Google 登录提供程序并启用它。
你可以在 [Firebase 认证文档](https://firebase.google.com/docs/auth/)中找到更多

## Firestore 数据库

回到 Firestore 项目，选择数据库->创建数据库

[![Create Database](img/ac7da831d85e1378d6bb378057ccc033.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a6G4x5nC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_800%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545922532/ajonp-ajonp-com/8-lesson-firestore-functions/ni4xs9bhwfsexfdf6jho.jpg)

我建议总是在锁定模式下开始，这有助于你了解在整个应用程序中你需要什么样的安全性，而不会忘记一些事情。有时它甚至有助于你的数据模型，因为一些安全设置实在是太糟糕了。

[![Firestore Lock Mode](img/0b8de8a01eab59afd0c3beb50da0c217.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v_Srvr3Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545922666/ajonp-ajonp-com/8-lesson-firestore-functions/qcgjr0ld7j9n9vyqji1n.jpg)

> 请注意，此时由于您的项目处于锁定模式，您将无法成功更新 Firestore 数据库，并且会看到失败，因为每次登录我们都会更新一条用户记录。

从项目
上传 Firestore 规则

```
firebase deploy --only firestore:rules 
```

Enter fullscreen mode Exit fullscreen mode

## Firebase 托管更新

### 到处更新项目名称

你可以做一个完整的项目查找和替换，查找`ajonp-lesson-8-admin`并用你的名字替换。

例我把我的从`ajonp-lesson-8-admin`改成了`ajonp-lesson-8-admin2`。如果您使用 VSCode，您可以像下面这样做。
T3![Find and Replace](img/e31d5bad7c1b6336fe9d0e08aa222143.png)T5】

### 更新样式

src/styles/ajonp-lesson-8-admin->您的姓名

如果你得到一个类似这样的错误，那是因为样式文件被改变了，我们只是交换了上面一行中的所有引用。

[![Style Error](img/0352d02cba2d62fc938bca18f2408590.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mN5ZMhoa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545921409/ajonp-ajonp-com/8-lesson-firestore-functions/hpldbf8rxw9a4f4c2ztj.jpg)

我把我的从`ajonp-lesson-8-admin-app-theme.scss`改成了`ajonp-lesson-8-admin2-app-theme.scss`。

> 此时，您可以测试从数据库中添加和删除书籍，但是我们仍然需要做一些准备工作来构建 Hugo 站点。

# 构建有角度的项目

目前我还没有开始使用 Ivy 渲染引擎，但是我希望它在将来可以更快地完成构建。当你跑完这一趟时，赶紧喝杯咖啡。

```
ng build --prod 
```

Enter fullscreen mode Exit fullscreen mode

结果
[![Angular Build Results](img/0e6895af246ee63c0e3d154cd7dcdcca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2iETZcBZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545923774/ajonp-ajonp-com/8-lesson-firestore-functions/jzryeqykv2bhtzqvyvem.jpg)

现在你的整个网站已经准备好托管，你可以尝试在本地运行`firebase serve`。

# 从 CLI 部署 Firebase 主机

现在我们在`dist/ajonp-lesson-8-admin2`有了一个产品角度构建，我们可以将它部署到我们的 Firebase 主机上，我们只需要创建一个授权的新站点。

## 创建新站点

托管->仪表板->高级选择“添加另一个站点”

```
firebase deploy --only hosting 
```

Enter fullscreen mode Exit fullscreen mode

结果
[![Hosting Deploy Result](img/606634e7930171b93030cd007d651705.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VmzooG3t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545924120/ajonp-ajonp-com/8-lesson-firestore-functions/bmvevp4ss4g85fnax9jc.jpg)

## 更新授权域

因为我们是从一个不同的域提供服务，你需要把它添加到我们的授权域。你可以在认证->登录方式->授权域中找到，选择“添加域”。

这将是`your-project-admin` .firebaseapp.com

你现在应该看到三个本地主机，默认(我们的 Hugo 网站)，你的项目管理网站。
[![Auth Domains](img/abe36372cce500c08fc6a9c251797a27.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--UnRHqrP7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545924515/ajonp-ajonp-com/8-lesson-firestore-functions/tjwkte0y4bcml9whonre.jpg)

# 更新 Firestore 触发器以匹配您的 GitHub repo

现在我们已经有了一个 Hugo 站点和一个 Angular admin 站点，我们可以设置一个 Firebase 函数，在每次添加/删除一本书时触发。

## 创建您的 Github 个人令牌

您需要[创建 Github 个人令牌](https://github.com/settings/tokens)，否则您将为每个请求发送您的密码。你可以在>设置- >个人访问令牌中找到这个，选择“生成新令牌”。这类似于密码，但是您可以限制令牌允许的访问。

为此，你可以给出回购和阅读:用户访问，命名一些有意义的东西。
[![Personal Token](img/a3b38093e84acf0d33ea0e6147f96435.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--dt_hQNLz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545925555/ajonp-ajonp-com/8-lesson-firestore-functions/suutevgobgp1hsgvja3k.jpg)

## 给 Firebase 功能添加 GitHub 个人令牌

您不能运行此命令来保存您的令牌，因为只有 Firebase 函数可以访问。在以后的课程中，我将展示如何更好地保护它们。

```
firebase functions:config:set git.token=your-token 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到这样的消息。
[![Firebase Config](img/68cb4caa451e0a0631c582b1a849146c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--pQTLhmWG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545925916/ajonp-ajonp-com/8-lesson-firestore-functions/w2goczy3ydk6ypbf1hny.jpg)

# 从 CLI 部署 Firebase 功能

现在部署您的函数，这将对 typescript 进行代码转换，并将 js 文件放在 lib 目录中。我将这两者都设置为在节点 8 上运行。

```
firebase deploy --only functions 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到一条成功消息
[![Successfully Deploy Functions](img/404c7cfc79c5e2bf06e2e1feeadcf94e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--he3k9mvs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545926029/ajonp-ajonp-com/8-lesson-firestore-functions/mneaigflqyxmqoqp557b.jpg)

# 添加一本书，观看它在 Hugo 网站的展示

> 警告您可能会出现争用情况，即您的第一个构建触发器发生在您的最后一个触发器之后。关于这一点，有一些工作我没有在本课中介绍。请记住，每次保存构建时，Google Cloud Build 都会重新生成整个 Hugo 站点。

在右下角你可以点击+ fab 按钮，这将打开一本新书的表单。
[![New Book Form](img/d54f8d164c72d92e5704966c0e2c453c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--NtHUUvf---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_500%2Cf_auto%2Cfl_lossy%2Cq_auto/v1545933791/ajonp-ajonp-com/8-lesson-firestore-functions/xyn381bujrehp021yeju.jpg)

一旦你点击保存，这将触发云功能`gitBookCreateHugoCommit`。检查[日志](https://console.cloud.google.com/logs/viewer)

[![Git Creat Log](img/dd13b91162fc5eaee58f97582cf93839.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wea1ZHrS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1545934369/ajonp-ajonp-com/8-lesson-firestore-functions/bcnequcgzehwyx9hjf1a.jpg)

在 [Google Coud Builder 历史](https://console.cloud.google.com/cloud-build/builds)中观察构建的发生

然后，您可以导航到您的 Hugo 网站(大约 2 分钟后)并查看新书。

## 删除一本书

除了会触发`gitBookDeleteHugoCommit`云函数之外，这与添加的作用是一样的。