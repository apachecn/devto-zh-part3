# 通过 AWS Amplify 控制台使用 Expo Web

> 原文：<https://dev.to/kayis/use-expo-web-with-the-aws-amplify-console-170m>

*[Flickr 上菲尔·罗德尔的封面图片](https://www.flickr.com/photos/tabor-roeder/)*

*更新 02.06.2019: Expo v33 预告出来了！现在 Web 版本可以与 Android 和 iOS 版本一起工作，所以我更新了这篇文章。*

我目前正在制作我的下一个视频课程，名为 **MVPs with AWS** 。它是关于用 AWS 技术构建最少的软件产品，重点是最实用的无服务器方法。

我为那门课做过客户访谈；他们透露，人们希望从一开始就构建跨平台的应用程序，所以我选择的前端技术是 Expo。Expo 让我们可以利用已经从网络上获得的 JavaScript 和 React 技能来构建 Android 和 iOS 应用程序。在 v33 版本中，他们还允许在 [react-native-web](https://github.com/necolas/react-native-web) 的帮助下构建 pwa。这种**通用应用**方法允许三个平台拥有一个前端代码库。

客户还关心持续交付和简化与 AWS 交互的工具，所以我选择 AWS Amplify 及其 SDK、CLI 和控制台。Amplify 提供了一个 JavaScript SDK，它可以很好地与 Expo 集成，CLI 有助于构建无服务器后端，而不需要从一开始就深入了解云的形成。使用 Amplify 控制台，我们可以在每次推送至我们的存储库时自动部署后端和前端。

**如果你想获得这个项目的更新[只要在推特上关注我](https://twitter.com/K4y1s)**

## 什么

我们将建立一个博览会项目配置为 Android，iOS 和网络建设与放大控制后端。

我们需要以下工具:

### [AWS Cloud9](https://aws.amazon.com/cloud9/)

可以通过浏览器访问的基于云的 IDE。它预装了 Node.js、npm 和 AWS SDK。如果我们想使用另一个编辑器，我们可以自己手动安装这些工具。

### [GitHub 资源库](https://github.com/)

连续交付由 Git 提交触发，因此我们需要一个 Amplify 控制台可以*监视*的存储库。

### [世博会](https://expo.io/)

我们使用 Expo CLI 来初始化一个项目，然后在此基础上构建一个 Android、iOS 和 Web 客户端。

### [AWS 放大](https://aws-amplify.github.io)

我们将使用 Amplify CLI 和控制台。CLI 用于向我们的 Expo 项目添加 Amplify 特性，控制台用于监视我们的 Git 存储库，以获取将触发构建和部署的提交。

## 如何

> **先决条件**
> 一个 AWS 账号和一个 Cloud9 环境。这是非常简单的，但是有困难的人可以看看这篇文章的前五个步骤。
> 一个 GitHub 账号。

设置将包括七个步骤:

1.  创建 Expo 帐户并登录
2.  初始化 Expo 项目
3.  添加脚本和依赖项
4.  初始化放大项目
5.  将放大文件提交给 Git
6.  设置和推送更改 GitHub 存储库
7.  设置并运行 Amplify 控制台构建

## 1。创建 Expo 帐户并登录

首先，第一步是创建一个 Expo 帐户，这样我们发布的 Android 和 iOS 项目将在 Expo 客户端上自动可用。

### 账户创建

世博会账户是完全免费的，可以在这里创建。

### 下载并登录世博客户端

我们需要为我们选择的移动平台安装 Expo 客户端。Android 客户端可以在 [Play Store](https://play.google.com/store/apps/details?id=host.exp.exponent) 中找到

安装完成后，我们需要用新创建的 Expo 帐户登录。这将使我们用 Expo-CLI 发布的项目在客户端自动可用。

### 登录 Expo-CLI

要使用 Expo-CLI 登录，我们只需运行 log in 命令，这将在我们的`~/.expo`中创建一些文件来跟踪我们的登录状态。

```
npx expo-cli login -u <USERNAME> -p <PASSWORD> 
```

## 2。初始化 Expo 项目

接下来，我们借助 Expo CLI 工具创建一个新项目。

```
npx expo-cli init universal-app --template blank@next 
```

我们给这个项目命名为“通用应用程序”。

## 3。添加脚本和依赖项

我们需要将`expo-cli`包安装为`devDependency`。在`package.json`中本地安装全局依赖项使得它们*可用，就像它们是 npm 脚本中的全局*一样。

```
cd universal-app
npm i -D expo-cli 
```

在撰写本文时，Expo 项目还没有构建或发布脚本；我们需要将此添加到我们的`package.json`中。

将`build`脚本添加到`package.json`的`scripts`部分:

```
{  ...  "scripts":  {  "login":  "expo login -u $EXPO_USERNAME -p $EXPO_PASSWORD",  "build":  "expo build:web",  "publish":  "expo publish --non-interactive",  ...  },  ...  } 
```

web 构建需要构建脚本，因此 Amplify Console 可以将其构建并部署到 AWS。

Android 和 iOS 构建需要发布脚本，因此 Expo 将在这些平台上构建并发布到 Expo 客户端。

由于它使用世博会的基础设施，它需要用户名和密码。

我们为此使用了环境变量，所以 Expo 凭证不会显示在日志中。

## 4。初始化放大项目

接下来，我们必须为我们的项目添加 Amplify 支持。Cloud9 使用与 Amplify CLI 期望的不同的文件名来存储 AWS 凭证，因此我们必须在初始化项目之前创建一个符号链接。

```
ln -s ~/.aws/credentials ~/.aws/config
npx @aws-amplify/cli init 
```

这会问我们几个问题。我们选择这些答案:

> ？输入项目名称 **universal-app**
> ？输入环境的名称**dev**T5】？选择你的默认编辑器:**无**
> ？选择您正在构建的应用类型 **javascript**
> 请告诉我们您的项目
> ？你用的是什么 javascript 框架**react-native**T15】？源目录路径:**/**/
> ？分发目录路径: **/web-build**
> ？构建命令: **npm 运行-脚本构建**
> ？开始命令: **npm 运行-脚本开始**
> 使用默认提供者**awscloudmformation**
> ？是否要使用 AWS 配置文件？**是**
> ？请选择您想要使用的配置文件**默认**

这将在云中创建一个基本的 Amplify 后端基础设施；IAM 角色、用于部署生成的云构造模板的 S3 桶等。

## 5。将放大文件提交给 Git

当我们将任何更改推送到远程 Git 存储库时，Amplify 控制台将构建我们的项目，所以在继续之前，我们需要将我们的更改放入 Git。

然后，我们可以添加所有新文件并进行初始提交:

```
git add -A
git commit -m "Init" 
```

## 6。设置并推送更改到 GitHub 存储库

可以在这里建立一个新的 GitHub 库。姑且称之为我们的项目**通用 app** 。为了简单起见，我们创建了一个**公共**存储库。

我们还需要在 Github 上为我们的 Cloud9 机器设置 SSH 认证。

密钥对可以通过以下方式生成:

```
ssh-keygen 
```

我们可以在我们的 Cloud9 机器上查找:

```
tail /home/ec2-user/.ssh/id_rsa.pub 
```

公钥需要被赋予一个标题并粘贴到[这里](https://github.com/settings/ssh/new)。

创建之后，我们可以推动我们的局部变化:

```
git remote add origin git@github.com:<GITHUB_USERNAME>/expo-web-app.git
git push -u origin master 
```

## 7。设置并运行 Amplify 控制台构建

现在我们已经在 GitHub 上创建、配置并启动了我们的项目，我们可以设置 Amplify 控制台来进行构建了。

为了连接，我们需要到[这里](https://eu-west-1.console.aws.amazon.com/amplify/home?region=eu-west-1#/create)。

我在这里用`eu-west-1`，但是你可以用你喜欢的任何地区。

选择 GitHub 作为提供者和我们创建的带有分支`master`的`universal-app`存储库。

在下面的表格中，我们被问到一些关于设置的问题。

> 您希望 Amplify Console 通过您的前端部署对这些资源的更改吗？

是的，我们想要。让我们选择我们用 Amplify CLI 创建的`dev`环境。如果我们想增加其他放大功能，这是必要的。

> 选择一个现有服务角色或创建一个新角色，以便 Amplify Console 可以访问您的资源。

我们需要为此创建一个新的 IAM 角色，但是这个过程是通过向导完成的。只有点击**下一个**直到完成。然后我们返回表单，刷新角色并添加我们新创建的`amplifyconsole-backend-role`

需要编辑构建设置。它们是自动创建的，但需要针对 Expo Web 版本进行一些调整。

```
version: 0.1
backend:
  phases:
    build:
      commands:
        - amplifyPush --simple
frontend:
  phases:
    preBuild:
      commands:
        - echo fs.inotify.max_user_watches=524288 | tee -a /etc/sysctl.conf && sysctl -p
        - npm ci
        - npm run login &
        - sleep 2s
    build:
      commands:
        - npm run publish --non-interactive
        - npm run build
  artifacts:
    baseDirectory: web-build
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
      - $(npm root --global)/**/* 
```

1.  增加系统的文件监视器，否则`expo publish`稍后会失败。
2.  `npm ci`运行，以便安装所有依赖项(包括`expo-cli`)。
3.  运行登录脚本，但不要等待它完成(&)，否则构建会冻结。
4.  休眠两秒钟，这样登录脚本就有一些时间来完成它的任务。
5.  构建并发布 Android 和 iOS 版本到 Expo 客户端
6.  建立网页版来放大

我们还需要为世博会 CLI 登录设置*环境变量*。我们必须在这里添加名为`EXPO_USERNAME`和`EXPO_PASSWORD`的变量，这些变量填充了我们在创建 Expo 帐户时使用的相应值。

之后，我们可以点击**下一个**，然后**保存并部署**。

## 奖励:推一个改动触发建造

现在一切都已启动并运行，我们可以更改一个文件，并将其推送到 GitHub 的远程存储库，并观察 Amplify 控制台的神奇效果。

让我们更改`App.js`
中的文本

```
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text>Awesome universal Expo app backed by AWS Amplify!</Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
}); 
```

添加更改，提交，并推送。

```
git add -A
git commit -m "Change text"
git push 
```

我们可以在这里观看建造。

## 结论

当 Expo 版本 33 发布时，我们将能够在 iOS、Android 和 Web 上使用一个代码库来利用 Expo，而不会牺牲原生 UI 小部件的功能。

Amplify 通过减轻管理 AWS 服务的痛苦，充分利用服务化无服务器计算的力量，并为 Android、iOS 和 Web 客户端提供一个后端，进一步加快了这一发展。

我相信这个堆栈将成为未来构建应用程序的坚实基础。

* * *

下面是[项目代码](https://github.com/kay-is/universal-app)