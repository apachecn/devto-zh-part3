# 基于 Github 操作的 Firebase

> 原文：<https://dev.to/w9jds/firebase-on-github-actions-1e64>

你可能听说过 Github 正在发布他们自己的工作流系统，它非常强大。它们甚至包括大量开箱即用的操作，可以为您完成很多事情，如部署到 npm、Azure 和 Google 云平台。然而，他们没有为 Firebase 提供任何东西，Firebase 提供了一整套服务，如托管、功能、实时数据库和 Firestore。

因为动作基本上可以是 docker 文件中的任何内容，所以我创建了一个 repo 来设置环境，这样您就可以在工作流中使用任何需要的 firebase-tools 命令。我一直在我的私有存储库上使用它来部署到 dev 和 prod 托管站点，因为我将它们推送到特定的分支。

在您的开发机器上，您需要从 Firebase 获得一个 CI 令牌，因此在您的终端中只需输入:

```
firebase login:ci 
```

这不会打乱您当前在计算机上的登录，并且会生成一个全新的令牌供您使用。

浏览登录过程，将发出的令牌复制到您的控制台中，并导航到您想要在其中包含 Firebase 操作的项目。在您的工作流内部创建一个新的动作，对于您想要将其设置为`w9jds/firebase-action@master`的用途(建议在 master 上使用一个特定的提交散列，因为它不会随着动作的更新而中断！).

在这个行动中有一个秘密部分。点击`create new secret`并使用您刚刚收到的令牌创建一个名为`FIREBASE_TOKEN`的令牌。完成后，请确保为此操作检查了密码。

根据您希望在此操作中执行的操作，将操作的参数更新为您想要的值。建议将操作默认为任何帮助命令，因此您希望覆盖它。一旦设置好，它应该看起来像这样:

```
action "Pushed to develop" {
  uses = "actions/bin/filter@95c1a3b"
  args = "branch develop"
}
action "Install Dependencies" {
  uses = "actions/npm@6309cd9"
  needs = ["Pushed to develop"]
  args = "install"
}
action "Build Development" {
  uses = "actions/npm@6309cd9"
  needs = ["Install Dependencies"]
  secrets = ["FIREBASE_API_KEY"]
  args = "run build"
}
action "Deploy to Development" {
  uses = "w9jds/firebase-action@master"
  needs = ["Build Development"]
  args = "deploy --only hosting:dev"
  env = {
    PROJECT_ID = "new-eden-storage-a5c23"
  }
  secrets = ["FIREBASE_TOKEN"]
} 
```

请注意，我有一个工作流，它首先安装我的项目所需的所有依赖项。然后，它继续运行项目的构建，最后将项目部署到我的 firebase 项目的开发托管目标。

一旦这个工作流运行，您应该会看到 firebase 操作输出如下:

```
=== Deploying to 'new-eden-storage-a5c23'...

i  deploying hosting
i  hosting[galaxyfinder-dev]: beginning deploy...
i  hosting[galaxyfinder-dev]: found 16 files in build
i  hosting: adding files to version [0/16] (0%)
i  hosting: uploading new files [5/6] (83%)
✔  hosting[galaxyfinder-dev]: file upload complete
i  hosting[galaxyfinder-dev]: finalizing version...
✔  hosting[galaxyfinder-dev]: version finalized
i  hosting[galaxyfinder-dev]: releasing new version...
✔  hosting[galaxyfinder-dev]: release complete

✔  Deploy complete! 
```

因为这个动作并不仅仅是为部署而设计的，如果这个命令被`firebase-tools`支持，那么你可以把它作为一个参数。

如果您想查看源代码和文档，下面是 firebase 操作:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[w9jds](https://github.com/w9jds)/[fire base-action](https://github.com/w9jds/firebase-action)

### 与 Firebase 交互的 GitHub 操作

<article class="markdown-body entry-content container-lg" itemprop="text">

# Firebase 的 GitHub 操作

这个针对 [firebase-tools](https://github.com/firebase/firebase-tools) 的操作支持使用`firebase`命令行客户端执行任意操作。

## 输入

*   `args` - **必选**。这是您希望用于`firebase` cli 的参数

## 环境变量

*   `FIREBASE_TOKEN` - **如果没有设置**GCP _ SA _ KEY，则需要。用于身份验证的令牌。这个令牌可以通过`firebase login:ci`命令获得。

*   `GCP_SA_KEY` - **如果 FIREBASE_TOKEN 没有设置**则需要。一个 base64 编码的私钥(json 格式),用于项目中具有`Firebase Admin`角色的服务帐户，如果您的部署功能还需要`Cloud Functions Developer`角色，并且由于部署服务帐户在部署过程中使用 App Engine 默认服务帐户，如果您只需要托管`Firebase Hosting Admin`就足够了，它还需要`Service Account User`角色。

*   `PROJECT_ID` - **可选**。要指定特定项目用于…

</article>

[View on GitHub](https://github.com/w9jds/firebase-action)