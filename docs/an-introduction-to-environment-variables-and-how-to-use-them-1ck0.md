# 环境变量及其使用方法介绍

> 原文：<https://dev.to/chingu/an-introduction-to-environment-variables-and-how-to-use-them-1ck0>

从应用程序中分离配置

<figure>[![](img/0724e59c2f51c71676e095d832c47f2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JeJm0FLL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AP7n5MULBV-fnwxuuu730FA.jpeg) 

<figcaption>照片由[安托万·道特里](https://unsplash.com/photos/05A-kdOH6Hw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍于 [Unsplash](https://unsplash.com/search/photos/math?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

### 什么是环境变量？

任何计算机编程语言的两个基本组成部分是 [*变量*](https://en.wikipedia.org/wiki/Variable_(computer_science)) 和 [*常量*](https://en.wikipedia.org/wiki/Constant_(computer_programming)) 。就像数学方程中的独立变量一样，这些变量的值会改变程序的结果。变量和常量都代表包含程序在计算中使用的数据的唯一内存位置。两者的区别在于变量值可能在执行过程中改变，而常量值是不变的。

一个 [*环境变量*](https://en.wikipedia.org/wiki/Environment_variable) 是一个其值在程序之外设置的变量，通常通过内置于操作系统或微服务中的功能来设置。环境变量由一个名称/值对组成，可以创建任意数量的名称/值对，并在某个时间点提供引用。

```
# Meteorite dataset from Nasa Open Data Portal
REACT\_APP\_METEORITE\_LANDING\_HOMEPAGE="https://data.nasa.gov/Space-Science/Meteorite-Landings/gh4g-9sfh"
REACT\_APP\_METEORITE\_STRIKE\_DATASET="https://data.nasa.gov/resource/y77d-th95.json" 
```

在应用程序初始化期间，它们被加载到`process.env`中，并通过在环境变量的名称后面加上后缀来访问，如下所示。

```
fetch(process.env.REACT\_APP\_METEORITE\_STRIKE\_DATASET)
.then((response) =\> {
 return response.json();
})
.then((strikesJSON) =\> {
 this.setState({ meteoriteStrikes: strikesJSON });
 this.setState({ isDataLoaded: true});
}); 
```

在运行时，对环境变量名的引用被替换为其当前值。在这种情况下，`process.env.REACT\_APP\_METEORITE\_STRIKE\_DATASET`被它的值`https://data.nasa.gov/resource/y77d-th95.json`替换。

环境变量的主要用例是限制由于配置数据的变化而修改和重新发布应用程序的需要。从上面的例子来看，当`REACT\_APP\_METEORITE\_STRIKE\_DATASET`的 URL 改变时，不需要修改源代码、测试和部署修改后的应用程序。

修改和发布应用程序代码相对复杂，并且增加了在生产中引入不良副作用的风险。当 URL 由环境变量而不是应用程序定义时，更改过程包括检查新 URL 的有效性、使用操作系统命令更新相应的环境变量或更新配置文件，以及测试受影响的应用程序功能以确保应用程序仍按预期工作。

环境变量的用例包括但不限于以下数据:

*   执行模式(例如，生产、开发、试运行等。)
*   域名
*   API URL/URI’s
*   公钥和私钥(仅在服务器应用程序中是安全的)
*   群组邮件地址，例如营销、支持、销售等邮件地址。
*   服务帐户名称

它们的共同点是它们的数据值很少改变，应用程序逻辑将它们视为常量，而不是可变变量。

接下来，让我们看看如何使用本机操作系统、NPM 包 dotenv 和 webpack 来利用环境变量。

### NodeJS 中的环境变量

<figure>[![](img/ced52246dbe8eafcd6d886cc9687acc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UyADNCZZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/520/1%2ABIgXzxgolWVDBNq5F_eZpg.png) 

<figcaption>图 1 — OS 环境变量</figcaption>

</figure>

在后端应用程序中使用环境变量依赖于操作系统命令来定义环境变量及其值。系统管理员可以使用命令行界面来定义这些，但是通过 shell 脚本来定义通常更有意义。环境变量通常不能在整个操作系统中全局访问，它们通常是特定于会话的。例如，使用 Linux 命令行:

```
setenv REACT\_APP\_METEORITE\_LANDING\_HOMEPAGE = "https://data.nasa.gov/Space-Science/Meteorite-Landings/gh4g-9sfh" 
```

在运行时，NodeJS 自动将环境变量加载到`process.env`中，使它们对应用程序可用。比如`fetch(process.env.REACT\_APP\_METEORITE\_STRIKE\_DATASET)`。

环境变量的管理和操作因操作系统而异。此外，这在不同的微服务环境中也会有所不同，比如 [Heroku](https://www.heroku.com/) ，在这里使用管理面板来管理环境变量。因此，在应用程序中使用环境变量之前，理解特定于平台的因素是至关重要的。

最小化这些差异的一个方法是使用[跨环境](https://www.npmjs.com/package/cross-env) NPM 包，它提供了一个与操作系统无关的 [POSIX](https://en.wikipedia.org/wiki/POSIX) 兼容的命令来设置环境变量..

### dotenv 包中的环境变量

支持在前端应用程序中使用环境变量不是浏览器或 Javascript 的“现成”特性；需要像 [*dotenv*](https://www.npmjs.com/package/dotenv) 这样的包才能启用。根据记录，前端和后端应用程序都可以使用 dotenv。

使用这个软件包非常简单

```
**import** dotenv **from**'dotenv';

dotenv.config();
console.log(process.env.REACT\_APP\_METEORITE\_STRIKE\_DATASET); 
```

这种技术通过将数据从源代码移动到一个`.env`文件中的环境变量来实现数据的外部化。将文件名`.env`添加到。gitignore 阻止 git push 命令将其上传到 GitHub repo，在那里，对于公共回购，任何人都可以使用它。

<figure>[![](img/549bec134358e2f10f4fb90b2d30e1ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VLEsGO0w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/341/1%2ARgk_Kcd5PKe5yySoYZ6E9w.png) 

<figcaption>图 2 —。env 文件用法</figcaption>

</figure>

`.env`中的环境变量被格式化为 name=value，以#开头的行被视为注释，空行被忽略。例如，

```
# Meteorite dataset from Nasa Open Data Portal
REACT\_APP\_METEORITE\_LANDING\_HOMEPAGE="https://data.nasa.gov/Space-Science/Meteorite-Landings/gh4g-9sfh"
REACT\_APP\_METEORITE\_STRIKE\_DATASET="https://data.nasa.gov/resource/y77d-th95.json" 
```

然而，许多流行的软件包如 Create React App (react-scripts)、Gatsby、GraphQL CLI、Node Lambda 和 [more](https://www.npmjs.com/browse/depended/dotenv) 已经包含 dotenv。如果您已经使用了这些软件包中的一个，dotenv 可能已经可以在您的应用程序中使用了。例如，上面的代码片段来自 Create React App 生成的一个应用程序，它要求环境变量以`REACT\_APP\_`为前缀。

在创建 React 应用程序的情况下，不需要调用`dotenv.config()`，因为`node\_modules/react-scripts/config/env.js`会在应用程序启动时自动用`.env`文件的内容填充`process.env`。关于创建 React 应用的例子，请参考 GitHub 上的[陨石探测器报告](https://github.com/jdmedlock/meteorite/tree/feature/07-create-issue)。

由于浏览器环境不安全，应用程序必须特别小心，不要暴露敏感信息，如应用程序机密。有关如何保护前端环境的更多信息，请查看“[保护应用资产:如何保护您的秘密](https://dev.to/jdmedlock/protect-application-assets-how-to-secure-your-secrets-295l-temp-slug-1679388)”。

### web pack 中的环境变量

webpack 是一个捆绑器，它将应用程序中许多不同的模块、资源和资产转换、捆绑或打包在一起，以便在浏览器中使用。webpack 的一个常见用途是为生产部署准备应用程序。例如，Create React App 的构建脚本使用 webpack 来创建包含应用程序生产版本的构建目录。

尽管 webpack 实现了对使用环境变量的支持，但它是作为 webpack 命令的一个[选项](https://webpack.js.org/guides/environment-variables/)来实现的。例如，

```
webpack --env.NODE\_ENV=local 
```

通过在 webpack 命令中指定多个- env 选项，可以支持多个环境变量。这些在 webpack 配置文件(例如 webpack.config.js)中被称为 env。以环境变量名为后缀。比如`console.log(env.NODE\_ENV)`。

webpack 配置文件也可能引用由操作系统使用`process.env`定义的环境变量，就像任何其他 Javascript 模块一样。请考虑 Create React App 中 webpack.config.prod.js 的这个示例。

```
// Source maps are resource heavy and can cause out of memory issue for large source files.

const shouldUseSourceMap = process.env.GENERATE\_SOURCEMAP !== 'false'; 
```

### 包装完毕

> “抽象将世界带入更复杂、可变的关系；它可以从看似虚无的事物中提取美、另类的地形、丑和强烈的现实。”—杰瑞·萨尔茨

使用环境变量是一种通过从代码中分离不经常改变的数据来使你的应用程序更容易配置的技术。尽管这种技术可能很简单，但它的使用受到应用程序类型(前端或后端)和操作环境(操作系统或微服务)等因素的影响。

利用环境变量很容易，但是理解它们的细微差别并能够有效和安全地利用它们是将有经验的 Web 开发人员与没有经验的开发人员区分开来的一个因素。与任何技术一样，诀窍不在于知道如何使用某样东西，而在于知道何时使用它。