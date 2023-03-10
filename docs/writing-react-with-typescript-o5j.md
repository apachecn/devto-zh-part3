# 写作与打字稿反应

> 原文：<https://dev.to/skurfuerst/writing-react-with-typescript-o5j>

今天，我无意中发现了一个很好的 GitHub 回购:

我们已经有了用 TypeScript 编写 React 应用程序的良好经验——我刚刚偶然发现了一个很好的备忘单，它总结了许多经验教训:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [打字稿-备忘单](https://github.com/typescript-cheatsheets) / [反应-打字稿-备忘单](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet)

### 面向有经验的 React 开发人员的 Cheatsheets

<article class="markdown-body entry-content container-lg" itemprop="text">

# react+打字稿备忘单

[![react + ts logo](img/8b2394706f13a1aaf4ef7db6597ff7c7.png)](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet/issues/81) 

面向有经验的 React 开发人员的 Cheatsheets

[**网络文档**](https://react-typescript-cheatsheet.netlify.app/docs/basic/setup) | [中文翻译](https://github.com/fi3ework/blog/tree/master/react-typescript-cheatsheet-cn) | [**西班牙语**](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet-es) | [投稿！](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet/blob/master/CONTRIBUTING.md) | [问！](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet/issues/new/choose)

* * *

<g-emoji class="g-emoji" alias="wave" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f44b.png">👋</g-emoji>本次回购由 [@swyx](https://twitter.com/swyx) 、 [@ferdaber](https://twitter.com/ferdaber) 、 [@eps1lon](https://twitter.com/sebsilbermann) 、 [@IslamAttrash](https://twitter.com/IslamAttrash) 、 [@jsjoeio](https://twitter.com/jsjoeio) 负责维护，我们很高兴您想用 React 试用 TypeScript！如果您看到任何错误或缺失，请[提交问题](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet/issues/new/choose)！<g-emoji class="g-emoji" alias="+1" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f44d.png">👍</g-emoji>

* * *

[![All Contributors](img/f618e39b50837007e55a53d421f4ac65.png)](https://raw.githubusercontent.com/typescript-cheatsheets/react-typescript-cheatsheet/master//CONTRIBUTORS.md)

## 所有反应+打字稿备忘单

*   **基本的备忘单** ( [`/README.md`](https://raw.githubusercontent.com/typescript-cheatsheets/react-typescript-cheatsheet/master//README.md#basic-cheatsheet-table-of-contents) )专注于帮助 React 开发者在 React **应用**中使用 TS
    *   专注于自以为是的最佳实践，复制+粘贴示例。
    *   解释一些基本的 TS 类型的用法和设置。
    *   回答最常见的问题。
    *   没有详细介绍泛型类型逻辑。相反，我们更喜欢教新手简单的故障排除技术。
    *   目标是在不学习太多 TS 的情况下有效地使用 TS。
*   **高级备忘单** ( [`/ADVANCED.md`](https://react-typescript-cheatsheet.netlify.app/docs/advanced/intro) )帮助显示和…

</article>

[View on GitHub](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet)

我们在 GitHub 样板文件(由我的好同事[罗伯特·巴鲁克](https://twitter.com/jamesalias)创建)中总结了我们自己在[沙尘暴](http://sandstorm.de)的最佳实践，在 GitBook 中总结了[的最佳实践:](https://sandstorm.github.io/typescript-react-app-kickstart-guide/)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [沙尘暴](https://github.com/sandstorm) / [打字稿-反应-app-kickstart-指南](https://github.com/sandstorm/typescript-react-app-kickstart-guide)

### 用 TypeScript 编写的 React Redux 应用程序的 Kickstart 指南

<article class="markdown-body entry-content container-lg" itemprop="text">

# react typescript kickstarter

这是使用预配置的 Redux、TypeScript、Storybook 和 testing 启动一个新的 React 项目的指南来源——所有这些都由 [docusaurus](https://docusaurus.io) 提供支持。如果你发现错误，不一致或想作出贡献:随时提交问题&拉请求。

## 先决条件

开始开发需要这些工具。

```
# node version management
brew install nvm

# node (latest lts version)
nvm install --lts

# yarn
brew install yarn --without-node
```

## 编者ˌ编辑

我们建议使用 [VSCode](https://code.visualstudio.com/) 作为编辑器。安装以下扩展:

*   `TSLint` - >林挺直接在编辑器中支持([市集](https://marketplace.visualstudio.com/items?itemName=eg2.tslint))
*   `Type safe React & Redux Snippets in TypeScript` - >样板代码片段，如《烹饪书》( [marketplace](https://marketplace.visualstudio.com/items?itemName=Sandstorm.vscode-awesome-ts-react-redux-snippets) )所示

## 食谱

在这本食谱中，你会找到重要模块的框架。这些框架也可以在 VSCode [marketplace](https://marketplace.visualstudio.com/items?itemName=Sandstorm.vscode-awesome-ts-react-redux-snippets) 中作为片段提供给 VSCode。

## 示例(带有 defaultProps 的组件)

> 你看，妈妈-智能感应！

[![component with defaultProps example](img/5a3d15d5023834e8948a2c3d66586bef.png)](https://raw.githubusercontent.com/sandstorm/typescript-react-app-kickstart-guide/mastimg/Component&defaultProps.gif)

</article>

[View on GitHub](https://github.com/sandstorm/typescript-react-app-kickstart-guide)

我们还发布了[一个 VSCode 片段扩展([https://marketplace.visualstudio.com/items?itemName = sandstorm . vs code-awesome-ts-React-redux-snippets](https://marketplace.visualstudio.com/items?itemName=Sandstorm.vscode-awesome-ts-react-redux-snippets))有助于用小样本编写 TypeScript/React。

你写 React + TypeScript 有哪些体验？

祝你一切顺利，塞巴斯蒂安