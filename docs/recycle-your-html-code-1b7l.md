# 回收你的 HTML 代码

> 原文：<https://dev.to/bsiscanu/recycle-your-html-code-1b7l>

软件开发的基本口头禅“不要重复自己”在 HTML 布局创建过程中很难实现，模板引擎平滑了流程，但它在很长一段时间内保持了痛苦和分歧的海洋。

令人高兴的是，随着 web 组件的引入，一切都改变了，web 组件是一组用于产生封装的 HTML 标签的 web 平台 API。它大大缩短了开发时间，提高了代码质量，并使 HTML 代码具有无限的可伸缩性。

显然，这是一个游戏改变者，为了说服你尽早采用 web 组件，我们启动了 Domy 项目，一个 web 组件的包管理器。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [队部](https://github.com/teamdomy) / [队部](https://github.com/teamdomy/domy)

### 自定义元素存储

<article class="markdown-body entry-content container-lg" itemprop="text">

# 自定义元素存储

[![License](img/d9722ea3fa238738a9da0f002093332f.png) ](https://github.com/teamdomy/domy/blob/master/LICENSE.md) [ ![Issues](img/dea4537726cd8cd77c86d5b2dd49587e.png)](https://camo.githubusercontent.com/5616ea5a67ab5f4248cda5079e7193e82d412ee4/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6973737565732f7465616d646f6d792f646f6d792e737667)

### 利用

使用您的`username`、`password`和`email`进行注册:

```
domy signup
```

从另一台计算机或使用另一个帐户登录系统:

```
domy login
```

使用模板编译项目，并在发布后提交整个目录:

```
domy publish --version 0.1.0
```

*执行你的项目的根(基)目录下的命令*
*版本可选。非版本化组件将被发送到**主**分支*

通过传递组件的类名可以只发布一个组件

```
domy publish MyComponent --version 0.1.0
```

使用组件的**类名**在另一个项目中安装组件:

```
domy install MyComponent --version 0.1.0
```

*已安装的组件将在 package.json `webcomponents`舱*中被跟踪

要安装 package.json `webcomponents`隔间中列出的所有组件，删除**类名**:

```
domy install
```

组件安装在 **node_modules/@** 目录中，可以在另一个**模板**应用程序中重用:

```
// master is a non-versioned
```

…</article>

[View on GitHub](https://github.com/teamdomy/domy)

它检测并发布模板项目中使用的每一个 web 组件，无需任何配置。编译后的代码主要代表 Stencil 为分发目的而生成的一组“中间”指令。这意味着在最后一英里，模板编译器应该被用来捆绑一切。在注册表中，组件存储在深度嵌套的集合中，其中根是当前用户名

目前，只支持类似模板的生成组件，LitElement 基类的集成即将到来。

我们很想听听你对这件事的看法。提交一个特性或者一个 bug 会更好。谢谢！