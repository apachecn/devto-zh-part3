# 如何在 Blockstack 上构建你的第一个区块链应用

> 原文：<https://dev.to/robghchen/how-to-build-your-first-blockchain-app-on-blockstack-2n01>

### 在去中心化网络上构建你的第一个应用的教程

[![](img/715b699d5c41658411eafe354e49e196.png)](https://medium.com/better-programming/start-building-blockchain-apps-now-a1ebe8f35ee7?source=rss-1a2b6738cba4------2)

###### Blockstack 是一个面向去中心化应用的网络。这个平台利用无服务器架构，帮助 ***移除*** 的关键漏洞点。通过消除这些经常成为黑客攻击受害者的弱点，Blockstack 使用户数据更加安全。

先决条件:本教程将需要 [React.js](http://reactjs.org/) 的知识。

区块链技术可能很复杂，但入门不一定如此。 [Blockstack](https://blockstack.org/) 的第三方登录/注册/认证使开发应用程序并将其发布到像 App.co[这样的分散式应用程序商店变得很容易](https://app.co/)

[![codecoderevolution](img/55244150d1ec27693093e2ded2631416.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CKAACUPO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A_bWaCp4pSYN8sZfH1xTxEQ.gif)

###### 不到一周，我就在 App.co 上发表了这篇文章

集成 Blockstack 后，您可以选择将用户从 Blockstack 创建的唯一用户名发送到您自己的 API，并以这种方式构建用户对象，或者使用 Gaia Storage，Blockstack 的分散后端提供商。你也可以选择将两者结合起来，像电话号码和地址这样的私人用户信息被加密并存储在 Gaia 存储中，而像评论或帖子这样的公共信息则存储在公共 API 中。

这篇博文旨在尽可能地简化和抽象。如果你想要深入的视频教程，请查看 YouTube 上的[技术集会](https://youtu.be/Arb-dwU5SZI)(这是我了解 Blockstack 的地方)。

现在，我们将介绍如何设置 Blockstack 登录/注销身份验证。我们开始吧！

1)安装 [Blockstack 浏览器](https://blockstack.org/install)

2)创建您的块堆栈 ID ( **确保将您的秘密恢复密钥保存在安全的地方** )
在您的终端:

```
npm init react-app blockstack-tutorial
cd blockstack-tutorial
npm install --save blockstack@19.0.0-alpha.2
npm install react-app-rewired --save-dev
mkdir src/utils
touch src/utils/constants.js
open src/utils/constants.js 
```

Enter fullscreen mode Exit fullscreen mode

如果`npm install`给你出了问题，试试`yarn add` :

```
yarn add blockstack@19.0.0-alpha.2
yarn add react-app-rewired --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

4) `constants.js` :

```
import { AppConfig } from 'blockstack'
export const appConfig = new AppConfig(['store_write', 'publish_data']) 
```

Enter fullscreen mode Exit fullscreen mode

5)在您的终端:

```
touch config-overrides.js
open config-overrides.js 
```

Enter fullscreen mode Exit fullscreen mode

6) `config-overrides.js`: