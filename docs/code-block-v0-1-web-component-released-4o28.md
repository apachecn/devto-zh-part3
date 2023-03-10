# 发布代码块 v0.1 web 组件

> 原文：<https://dev.to/justinribeiro/code-block-v0-1-web-component-released-4o28>

在过去的几个月里，我收到了几封电子邮件，询问我是如何让我的代码样本在这个博客上工作的。在过去，这可能是一个更难回答的问题(我最初的 web 组件并不理想)，但随着我最近的春季更新，我认为这将是一个很好的突破。

宣布`<code-block>`，一个基于 [LitElement](https://lit-element.polymer-project.org/) 的 web 组件，利用 [Prism.js](https://prismjs.com/) 。

[![pre in action](img/df9ca4dec37092d4917eb212dd15b16d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GAQbRv1t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/jdr-public-imgs/blog/20190416-code-block-custom.jpg)

在 [npm](https://www.npmjs.com/package/@justinribeiro/pre) :
有售

```
$ npm i @justinribeiro/pre $ yarn add @justinribeiro/pre 
```

或者可以从[回购](https://github.com/justinribeiro/pre) :
中抢

```
$ git clone git@github.com:justinribeiro/pre.git 
```

从那里开始，像任何 html 元素一样在元素中编写代码。

```
\<pre language="fortran" theme="/node\_modules/prismjs/themes/prism-okaidia.css"\> program HelloWorld write (\*,\*) 'Hello, world!' ! This is an inline comment end program HelloWorld \</pre\> 
```

和我在这个博客上运行的版本一样，这个组件使用动态语言导入，这样你就不必在你的包中增加额外的重量(只加载你需要的)。v0.1.0 版本还包括对主题的支持，所以你只需将主题 CSS 文件的路径传递给它，它就会完成剩下的工作。

这种设置的最大障碍是你的*有*来确保你带着`/node_modules/prismjs/**`，否则加载就不能正常工作。我考虑过替代方法，但主要是让我复制了 Prism 的大部分语言支持，这似乎很不明智。

在未来，我想看看我是否能启用 Prism 庞大的插件支持。同时，抓住它，在某处嵌入一些代码，web 组件风格。