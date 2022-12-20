# 使用 Redux 的 Web 组件

> 原文：<https://dev.to/kozakrisz/web-components-with-redux-a9j>

所以，我写帖子已经有一段时间了。我把时间花在工作上，收集新事物的经验，比如本地 Web 组件。我和他们一起创建了一个实验项目，现在我想向你们展示一下。

你可以在我的 [GitHub 简介](https://github.com/krisztiankecskes/counter-native-web-component-redux)中找到该项目的源代码。

## 🔎简短描述

我的目的地很明确:

*   创建本机 web 组件
*   在 HTML 文件中多次使用它
*   我想使用相同的组件，但不同的值显示在他们里面
*   在 Redux 的帮助下将它们连接到一个共同的商店

我想使用 ES6 的特性，所以我决定使用 Webpack 和 Babel。Webpack 负责将流程和开发服务器功能与 webpack-dev-server 捆绑在一起。

剩下的很轻。我创建了一个计数器组件，它有一个显示数字的显示屏，在它下面有一个按钮可以帮助你增加数字。我在 HTML DOM 中插入了三个 times counter 组件，并将它们连接到一个公共存储。

Redux 存储准备好获取三个带有 ID-s 的计数器值，所以我可以单独处理每个计数器组件的值。

主应用程序模块获得页面上的每个计数器值，并与它们累计值，并显示在计数器上方。

*🎉仅此而已！*

你可以试一试这个项目，随意克隆它！🙂