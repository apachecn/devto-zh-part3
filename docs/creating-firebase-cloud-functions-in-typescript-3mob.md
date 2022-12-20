# 在 TypeScript 中创建 Firebase 云函数

> 原文：<https://dev.to/jakenherman/creating-firebase-cloud-functions-in-typescript-3mob>

> 这篇文章最初分享在我的网站[jakenherman.com](https://www.jakenherman.com/articles/2019-02/firebase-cloud-func-typescript)上。

Firebase 太棒了。我们就从这个开始吧。超级便宜，超级容易设置和使用，并提供了大量的功能。话虽如此，让我们看看如何在 TypeScript 中为 Firebase 创建云函数。

## 什么是云函数？

云函数是在安全的托管 Node.js 环境中执行的单一用途 JavaScript 函数。基本上，它们允许你创建一个移动后端，而不必处理管理服务器的麻烦。但是等等，这里说的是单一用途的 *JavaScript* 函数，而不是*类型脚本*。是的，这没什么大不了的——TypeScript 只是一个 JavaScript 超集，它可以转换成 JavaScript，所以我们可以使用它！云函数的酷之处在于，它们只需一个命令就可以推送到 Firebase 服务器。之后，云功能会自动扩展计算资源，以匹配应用的使用模式。您不必担心 SSH 凭证、服务器配置、配置新服务器或淘汰旧服务器。

## 我们开始吧

假设你已经创建了一个 firebase 帐户，上面有一个应用程序(如果你还没有，创建一个非常容易，所以我不会为了这篇文章的目的而浏览它)，打开一个新的终端，导航到你希望你的 Firebase 云功能所在的任何目录(或者创建一个新的[ `mkdir cloud-functions` ])，然后键入`firebase login`。如果你得到一个错误，说 firebase 不是一个可识别的 cmdlet 或类似的东西，只需运行`npm install -g firebase`。如果你必须运行安装命令，要么重启你的终端，或者如果你使用 Powershell 或 MS 命令行，只需运行`refreshenv`并再次运行`firebase login`。您将被定向到您的 web 浏览器，以登录到您的 Firebase 控制台。登录，回到您的终端并运行`firebase init`。您将得到一个提示，说“您将要在这个目录中初始化一个 Firebase 项目:您准备好继续了吗？”键入`Y`继续，你会看到 5 个选项:数据库、Firestore、函数、托管和存储。如果有人感兴趣，我们可以看看另一篇博客文章中的一些其他选择(留下评论)，但现在我们只想要函数。按下键盘上的向下按钮，直到到达`Functions`，然后按下键盘上的“Space”进行选择，并点击“Enter”。现在，系统会提示您选择要为其创建云函数的项目。如果您还没有一个项目，您可以从该窗口创建一个新项目，或者选择一个您已经拥有的项目。下一个问题是你想使用 JavaScript 还是 TypeScript，我们显然会选择 TypeScript，所以选择它。下一个选项完全由您决定——它询问您是否想使用 TSLint 来捕捉可能的 bug 和样式强制。我对 TSLint 的看法是，它只能有所帮助，肯定不会有坏处，我十有八九会用它。一旦您选择了 TSLint 选项，将会创建一些`.json`文件和您的`index.ts`文件，为您提供以下项目结构:

```
myproject
 +- functions/  
      |
      +- package.json  # npm pkg file
      |
      +- tsconfig.json
      |
      +- tslint.json # Optional - if you opted out of tslint, this wont be here
      |
      +- src/     # Directory containing TypeScript source
      |   |
      |   +- index.ts  # main source file for your Cloud Functions code
      |
      +- lib/
          |
          +- index.js  # Built/transpiled JavaScript code
          |
          +- index.js.map # Source map for debugging 
```

Enter fullscreen mode Exit fullscreen mode

您将被提示最后一个问题-是否要立即安装 npm 的依赖项？键入`Y`，因为我们最终需要安装这些依赖项，所以最好现在就安装！

## 写&部署

一旦 firebase 项目初始化完毕，在您选择的代码编辑器中打开它，查看默认的 TypeScript 代码。它被注释掉了，但是它向您展示了一个 helloWorld 云函数的好例子，如下所示:

```
import * as functions from 'firebase-functions';

export const helloWorld = functions.https.onRequest((request, response) => {
 response.send("Hello from Firebase!");
}); 
```

Enter fullscreen mode Exit fullscreen mode

取消注释，让我们把它发送到我们的项目。请注意，这是您创建函数的方式。如果你想要一个将两个数相加的函数，你可以这样做:

```
export const addNumbers = functions.https.onRequest((request, response) => {
  response.send(Number(request.query.a) + Number(request.query.b));
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们将这些发送到 firebase 应用程序。打开你的终端，输入`firebase deploy`。一旦部署完毕，你可以在 Firebase 控制台的“云功能”部分看到它们。然后你可以从你的浏览器给他们打电话，或者按你喜欢的方式 ping 他们。云功能非常灵活，我强烈建议尝试一下。