# 面向 yahoo/react-intl 的国际化云工具

> 原文：<https://dev.to/pelirroja/internationalization-cloud-tool-for-yahoo-react-intl-1bh1>

你是否在努力保持你的应用程序翻译最新，而你的梦想充满了要翻译的空键？别担心，我们非常了解那种感觉。为了使整个本地化过程更容易，我们创建了一个简单有效的解决方案，它将提高翻译质量并帮助您保持正确的方向！

[![cli](img/24ae9075226cd97153acbf518b4f1cbd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xk7CAVBM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8stvdc832sfg94vu1cuv.jpeg)

# 它是如何工作的？

SimpleLocalize 是一款帮助开发者管理应用翻译的工具。您可以使用导入工具将项目中的所有关键字上传到云编辑器中，并将其转换为简单干净的电子表格，这就是您所要做的一切！翻译后的密钥将自动与您的项目同步，反之亦然，当您添加新密钥时，它将出现在 SimpleLocalize cloud 中，准备添加翻译。你可以**使用 SimpleLocalize-CLI 在源代码中自动查找国际化关键字**，也可以手动导入导出文件，我们支持多种格式，像 [yahoo/react-intl](https://github.com/yahoo/react-intl) ，Android XML 字符串资源，iOS，Jekyll，Java 属性，当然还有 Excel 和 CSV 文件。听起来很有趣？

[![flow](img/1a20e24e83687a3073fa40791bee764a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DWphnioL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ui8ddirj973pcujlwdsj.jpg)

# i18n 键自动发现功能

创建 SimpleLocalize 是为了使您的工作自动化，并帮助您更新所有翻译。您可以创建多个项目，**与您的团队共享它们**或者将选定项目的访问权授予翻译团队。当他们的工作完成后，你可以**发布对 CDN** 的更改，刷新你部署的应用，瞧！现在什么都翻译了！您可以轻松检查项目翻译的进度，跟踪丢失的关键字，并使用任意多种语言。由于专业和不断更新的翻译，使您的应用程序在全球可用。

# 如何开始？

首先，登陆 SimpleLocalize.io 和[注册一个账户](https://app.simplelocalize.io/register)，这是一个免费的基本计划，允许你处理中等规模的翻译库。**通过添加名称创建一个新项目**。**选择项目类型**，它将定义翻译导出的方式，例如，对于 ReactJS 和 [yahoo/react-intl](https://github.com/yahoo/react-intl) 这样的 web 应用程序，只有 CDN 是最常见的。

[![create-project](img/90b61010c5f6242ae8ddc470c067c664.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cp7seAod--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hxkq4x1uqtoxnx0qwuaw.png)

**打开项目，进入*设置*。**一键下载配置属性*下载 CLI 属性*:

[![simplelocalize-download-credentials](img/2a8ad4bc924da2ec678a9c87ef1af8eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zLYipQmb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k752b3gx38nqe8lrmc5d.jpeg)

在下载的文件中，将`projectType:`属性设置为`yahoo/react-intl`，它应该看起来像下面这样:

```
uploadToken: <PROJECT_UPLOAD_TOKEN>
projectType: yahoo/react-intl 
```

Enter fullscreen mode Exit fullscreen mode

[*阅读更多关于 CLI `projectType`属性*的信息。](https://simplelocalize.io/docs/cli/get-started/)将文件保存在你的项目根目录下。接下来，在您的项目目录中使用命令行运行 CLI 工具:

```
curl -sL https://cdn.simplelocalize.io/cli/simplelocalize | bash 
```

Enter fullscreen mode Exit fullscreen mode

它将根据配置文件中定义的`projectType`在您的项目目录中搜索关键字，并将它们发送到 SimpleLocalize cloud，在那里您可以轻松地检查有多少翻译丢失，并在一个地方管理它们。

[![spreadsheet](img/368bac14734e3d680783f8d506fe9c63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D66Fzfyd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p6vokj8q5zlyrz2mzr7m.png)

完成，您成功地配置了项目！

添加翻译并发布更改，以检查项目中的关键字是如何更新的！**当您点击*发布*按钮时，所有翻译将通过 CDN** 发送到您的前端应用程序，即使应用程序已部署，它也能工作。这意味着您可以更改页面上的文本，而无需重新构建和重新部署整个应用程序。很有用，是吧？

现在您可以使用 url 获取翻译，如下所示:

[https://cdn . simple localize . io/:project token/_ latest/:language key](https://app.gitbook.com/@simplelocalize/s/simplelocalize/api/generic-cdn)

或者使用 [`react-intl-simplelocalize`库](https://simplelocalize.io/docs/integrations/format-js/)进行 ReactJS 应用。

[![project-list](img/f67de950548f6e813023b8281bc48167.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pETrs6sK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fr5kjtvxd1ftr45188ys.png)

# 准备开始了吗？

试一试，测试我们创造的新国际化工具。它旨在帮助您将所有翻译保持最新，轻松管理新的关键字和翻译更改，并在一个位置同时处理多个项目。将您的 web 或移动应用程序与 SimpleLocalize 集成，无需 Excel 电子表格和翻译文件即可高效地与您的客户合作，而是使用 SimpleLocalize 的干净且用户友好的界面，并以更加愉快的方式控制 i18n 流程。
[创建账户，不需要信用卡！](https://app.simplelocalize.io/register)