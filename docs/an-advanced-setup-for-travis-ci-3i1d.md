# Travis CI 的高级设置

> 原文：<https://dev.to/rampa2510/an-advanced-setup-for-travis-ci-3i1d>

在这篇博文中，我将引导您了解 YAML 文件的基本知识，然后我们将编写一个示例. travis.yml 文件，我试图使它尽可能通用，以便您可以更有效地使用 Travis CI。此外，这篇博客文章是写给那些熟悉 CI/CD 和测试概念的人的。

## 什么是 YAML？

YAML 是“YAML 不是标记语言”的递归首字母缩略词。像 JSON 一样，它也是一种数据序列化语言(也就是说，它可以用来在网络上存储或传输数据)

## YAML 和 JSON 的一些区别

[![differences between YAML and JSON](img/5651d9826758762585af0ae8550f302d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yy-VkD5Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2tsm1x9k3x0r7nngps4u.png)

## YAML 语法

我将介绍一些基本语法，帮助你入门，但是如果你想了解更多，请参考这个[链接](https://yaml.org/refcard.html)

### YAML 的基本语法是

键值对使用 **":"**
分隔

```
key: value 
```

您可以使用 **"#"**
进行评论

```
# this is a comment in YAML
key: value 
```

这些知识对于我们的例子来说已经足够了。现在让我们从. travis.yml 文件开始

## Travis 配置文件

请注意这一点，因为 YAML 文件有严格的缩进规则。

### 编程工具

我们开始用将要使用的语言配置我们的. travis.yml 文件。例如，我将为我的 MERN 堆栈应用程序创建这个 Travis 文件，因此我将选择我的语言 nodejs 来测试我的后端代码。

```
language: node_js
node_js:
  - "11" 
```

现在，我们告诉 Travis CI 我们希望我们的环境是 nodejs，我们还指定了它的特定版本。我用的是 v11，你可以使用任何你想要的版本，你可以通过在下面提到它们来测试多个版本

```
language: node_js
node_js:
  - "11"
  - "12" 
```

你也可以在 nodejs 的当前稳定版本上测试这个，比如

```
language: node_js
node_js:
  - "stable" 
```

### 构建环境

我们可以指定我们希望 Travis 在其中执行测试的发行版/操作系统/构建环境。我推荐使用 trusty，因为这个发行版是轻量级的，更可靠。虽然这是 Travis CI 设置的默认发行版，但我将向您展示配置，以便您可以使用 Travis 提供的任何发行版。

```
dist: trusty 
```

Travis 提供的其他发行版有

*   Ubuntu Xenial 16.04
*   Ubuntu Precise 12.04

### 服务

很多时候，我们有各种各样的服务与我们的项目交互。我们需要包含它们来完整地测试我们的项目。您可以选择的一个选项是集成服务的在线设置，但这将增加测试所需的时间，而且如果服务是付费的，您将不得不为您的服务的每个请求支付额外的费用。因此 Travis ci 提供了一个解决方案，它为您提供了在构建中安装所需服务的选项，并根据您的需要进行配置。
我们可以配置许多服务，但这篇文章不是关于它的，所以
我将向你展示如何为 CI 配置 MongoDB。

```
services: mongodb 
```

对于任何您想要告诉 Travis 安装依赖项的服务，您必须使用键`services`。现在，您可以像在本地一样进行配置，例如-

```
var url = "mongodb://localhost:27017/mydb";

MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  console.log("Database created!");
  db.close();
}); 
```

就是这样，你必须连接到本地主机的 27017 号端口。只需遵循与您想要连接的服务相同的默认连接说明。您可以[在这里](https://docs.travis-ci.com/user/database-setup/)查找其他服务。

### 环境变量

您可以使用`env`键，例如-
，设置您想要测试项目的环境变量

```
env:
  - NODE_ENV=ci PORT=3000 
```

### 隐藏物

您可以在您的构建中缓存目录，这样您就不必在每次构建时都下载它，因为那样会耗费您的时间，所以您可以使用
来指定您想要缓存的目录

```
cache:
  directories:
    - node_modules 
```

在这里，我想为每次构建缓存我的 node_modules 目录，这样我就不必在每次构建时都下载它，当我添加或删除 node_module 时，Travis 只需比较 package.json 并进行必要的更改，然后再次缓存它

### 剧本

您希望在配置 Travis CI 之前或之后安装各种依赖项。或者您可能需要在构建开始执行您的测试之前运行各种脚本。您可以安装或运行它们，方法是在执行前使用关键字`before_install`运行命令，在 Travis 完成配置构建后使用关键字`install`安装依赖项
eg -

```
before_install:
  - sudo apt-get install -y libxml2-dev
install:
  - npm install 
```

因此，在安装依赖项之前，我想添加一个开发库，之后，我想运行脚本来为我的项目安装依赖项。

此外，您可以在测试执行期间使用`script`关键字 eg -
运行各种脚本

```
script:
  - npm run start 
  - npm run dev 
```

这些是一些高级设置，您可以使用它们来配置 Travis，使您的工作变得轻松，并有效地使用 CI。

我很想听听你们用的更先进的设置，或者你们对这个博客的反馈，我会很感激的。感谢您的阅读，如果我能通过这篇博文向您传授新的知识，我将非常乐意听到。

你可以在 twitter 或 LinkedIn 与我联系。

我在网上的名字是 [@ram2510](https://www.google.com/search?q=ram2510)