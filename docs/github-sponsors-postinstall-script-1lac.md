# GitHub 是 npm 的赞助商

> 原文：<https://dev.to/ffloriel/github-sponsors-postinstall-script-1lac>

Github 赞助商已经在 5 月 23 日柏林 GitHub 卫星期间宣布。这是开源开发者从社区募集捐款的一种新方式。

对于维护 npm 包的 javascript 开源开发者来说，有一个简单的方法可以提高赞助意识。

[NPM 包 github-赞助商](https://github.com/Ffloriel/github-sponsors)允许开源维护者轻松地为他们的包打印安装后消息。`github-sponsors`正在使用 FUNDING.yml 检索信息并显示给用户。

当用户安装你的软件包时，会出现一条简单的消息。
[![Donation message](img/bd9c01d716c90164da2708993b1f950e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--9i_c0Gds--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/Ffloriel/github-sponsors/raw/master/docs/assets/example-donation-message.png)

用这个包，我创建了一个小网站。我希望提供一些有用的信息，关于开源开发者可以使用 GitHub 的不同融资模式平台。对于寻找赞助商的人来说，这可能是一个很好的资源。

我不太了解`FUNDING.yml`文件中支持的一些平台。如果任何人想添加信息，下面是知识库的链接。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [【蛋清】](https://github.com/Ffloriel) / [ github 赞助商](https://github.com/Ffloriel/github-sponsors)

### “npm 安装后提示您的用户捐赠”

<article class="markdown-body entry-content container-lg" itemprop="text">

# Github 赞助商

[![Build Status](img/035cd7a1a16dc2b31324eb46a4f8664d.png)](https://raw.githubusercontent.com/Ffloriel/github-sponsors/master/)[![CircleCi](img/4fa303408ef468490c47549f6d07464c.png)](https://raw.githubusercontent.com/Ffloriel/github-sponsors/master/)[![dependencies Status](img/315afb6b53359fb214c54259ece576e6.png)](https://david-dm.org/ffloriel/github-sponsors)[![devDependencies Status](img/71111e2c19bdb657e488392d108d0077.png)](https://david-dm.org/ffloriel/github-sponsors?type=dev)[![Codacy Badge](img/958e7ec0b74e83666fc4a2db2cd1ccf4.png)](https://www.codacy.com/app/florielfedry/github-sponsors?utm_source=github.com&utm_medium=referral&utm_content=Ffloriel/github-sponsors&utm_campaign=Badge_Grade)[![Codacy Badge](img/4af0fd2ff3c767f385fa94dc6beb9a86.png)](https://www.codacy.com/app/florielfedry/github-sponsors?utm_source=github.com&utm_medium=referral&utm_content=Ffloriel/github-sponsors&utm_campaign=Badge_Coverage)[![styled with prettier](img/283916b730a6272e7a62f674e68cb747.png)](https://github.com/prettier/prettier)[![npm](img/ffaf8adc364bf53feb7931a7093a411a.png)](https://www.npmjs.com/package/github-sponsors)[![license](img/fa6d0f6d2b3784c426adfaaff7ece14a.png)](https://raw.githubusercontent.com/Ffloriel/github-sponsors/master/)[![dependabot](img/c749f04b58cf5e83bc62b1ec9b6451eb.png)](https://raw.githubusercontent.com/Ffloriel/github-sponsors/master/)

[![Mona logo](img/55ee89dc8ec093c47548f25ebc9c9466.png)](https://camo.githubusercontent.com/6f2131e993baae7b9350775b79149c31c28657dc/68747470733a2f2f6769746875622e6769746875626173736574732e636f6d2f696d616765732f6d6f64756c65732f736974652f73706f6e736f72732f6c6f676f2d6d6f6e612e737667)

## 什么是 Github 赞助商？

Github 赞助商已经在 5 月 23 日柏林 GitHub 卫星期间宣布。对于开源开发者来说，从社区收集捐赠是一种新的方式，有几种方式已经存在，但 GitHub 赞助商在这方面承担责任，直接在 GitHub 上添加这个功能。你可以在你的项目顶部添加一个赞助商按钮，它会打开一个包含捐赠页面链接的弹出窗口。

这个包允许开源维护者轻松地为他们的包打印一个安装后消息。`github-sponsors`正在使用 FUNDING.yml 检索信息并显示给用户。

[![Donation message](img/51a4c01bbd0f8a8ae90b51b1a2287100.png)](https://raw.githubusercontent.com/Ffloriel/github-sponsors/master/./docs/assets/example-donation-message.png)

## 入门指南

### 装置

```
npm install --save github-sponsors 
```

### 使用

Add in package.json:

```
{
  "scripts": {
    "postinstall": "github-sponsors"
  }
}
```

## 关于 GitHub 赞助商

*   [宣布 GitHub 赞助商](https://github.blog/2019-05-23-announcing-github-sponsors-a-new-way-to-contribute-to-open-source/)
*   [GitHub 赞助商](https://github.com/sponsors)
*   [关于 GitHub 赞助商](https://help.github.com/en/articles/about-github-sponsors)
*   [赞助公开赛](https://help.github.com/en/articles/sponsoring-open-source-developers) …

</article>

[View on GitHub](https://github.com/Ffloriel/github-sponsors)

网址:[链接](//ffloriel.github.io/github-sponsors/)