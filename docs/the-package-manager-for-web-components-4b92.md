# React 组件的包管理器

> 原文：<https://dev.to/bsiscanu/the-package-manager-for-web-components-4b92>

**更新:Webpack 已被弃用，取而代之的是模板编译器(0.5.0 版)**

我们为 React 组件发布了零配置包管理器的 alpha 版本。

它帮助开发人员直接从现有应用程序中发布和重用任何组件，不需要元数据或配置文件。基本上，Domy 将重用您的主应用程序的配置数据来传输组件(例如 webpack.config.js)。

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

使用组件的**类名**将组件安装到另一个项目中:

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

当前版本仅支持反作用组件，暂时不支持角组件或腹板组件。它在阿尔法测试阶段之后到来。

为了开始进入 Domy 软件包管理器的神奇世界，首先安装它:

***NPM install-g domy-CLI*T3】**

...并通过执行以下操作创建您的个人帐户:

***标志住宅***

它会询问你的用户名、密码和电子邮件。

然后，导航到您的最佳 React 应用程序的根目录，并将所需的组件发布到注册表。Domy 将检测您的主应用程序的配置，并将其应用于组件打包过程:

***domy 发布`<`名称`>` `<`文件`>`***

在上面的例子中，我们使用尖括号作为预期参数的例子。在我们的例子中，它是一个组件的名称和存储它的文件的路径。该路径相对于应用程序的根目录。

最后，导航到启动了 npm package.json 的另一个项目的根目录，并执行命令 install:

***domy 安装`<`名称`>`***

它将在你的项目的根目录下创建新的组件目录，并将组件放在那里。安装的组件可能需要作为一个简单的本地文件/模块。

还有什么？我想现在就这样吧。来吧，试试我们令人敬畏的库，又名包管理器，不要忘记在 Github 上给我们一颗星。谢谢