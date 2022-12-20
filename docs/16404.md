# 宣布 dotenv-linter:一个给你的。环境文件

> 原文：<https://dev.to/wemake-services/announcing-dotenv-linter-a-tool-to-lint-your-env-files-3m1g>

我很高兴地宣布一个简单但非常有用的工具来 lint 你的`.env`文件。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[we make-services](https://github.com/wemake-services)/[dotenv-linter](https://github.com/wemake-services/dotenv-linter)

### ☺️林挺 dotenv 文件像一个魅力！

<article class="markdown-body entry-content container-lg" itemprop="text">

# 多滕夫林特尔

[![wemake.services](img/af5f15014c9d41f5c2e76d01102852a9.png)](https://wemake.services)[![Build Status](img/b4c9d9c50f0bde6fd8f3b855d522d797.png)](https://github.com/wemake-services/dotenv-linter/actions?query=workflow%3Atest)[![codecov](img/0aeb9a07837a86b05e2732eae57cd0ed.png)](https://codecov.io/gh/wemake-services/dotenv-linter)[![Github Action](img/9190e6608b302a24e55a616e3459c350.png)](https://github.com/wemake-services/dotenv-linter/actions?query=workflow%3Adotenv)[![Python Version](img/f3cc9b539bd1e25c06eb511592db7947.png)](https://pypi.org/project/dotenv-linter/)[![Documentation Status](img/e28e5bbd7c044c41f513e92295a90275.png)](https://dotenv-linter.readthedocs.io/en/latest/?badge=latest)

* * *

用于`.env`文件的简单 linter。

[![dotenv-logo](img/c3f9d7c5ea5c4428afa20f344f3b954f.png)](https://raw.githubusercontent.com/wemake-services/dotenv-linter/master/docs/_static/dotenv-logo@2.png)

虽然`.env`文件非常简单，但需要保持它们的一致性，该工具提供了广泛的一致性规则和最佳实践。

它可以完美地集成到任何现有的工作流程中。

阅读[公告帖](https://sobolevn.me/2019/01/announcing-dotenv-linter)。

## 安装和使用

```
pip install dotenv-linter
```

Enter fullscreen mode Exit fullscreen mode

然后运行它:

```
dotenv-linter .env .env.template
```

Enter fullscreen mode Exit fullscreen mode

更多信息参见[用法](https://dotenv-linter.readthedocs.io/en/latest/#usage)部分。

## 例子

您的`.env`文件中可能会出现许多错误:

```
# Next line has leading space which will be removed:
 SPACED=
# Equal signs should not be spaced:
KEY = VALUE
# Quotes won't be preserved after parsing, do not use them:
SECRET="my value"

# Beware of duplicate keys!
SECRET=Already defined ;(

# Respect the convention, use `UPPER_CASE`:
kebab-case-name=1
snake_case_name=2
```

Enter fullscreen mode Exit fullscreen mode

还有更多！您可以在我们的文档中找到[完整的违规列表。](https://dotenv-linter.readthedocs.io/en/latest/pages/violations/)

…</article>

[View on GitHub](https://github.com/wemake-services/dotenv-linter)

## 背景

作为我们企业文化[“不责备而是改正”的一部分](https://dev.to/sobolevn/building-blameless-working-environment--17hl)，我们建立了许多工具来防止我们一次又一次地犯同样的错误。

`dotenv-linter`就是这些工具之一。

前一段时间，我们遇到了几个关于`.env`文件的问题:

*   一些开发者使用`CONSTANT_CASE`作为变量名，一些开发者使用`snake_case`作为变量名。虽然这不是一个技术问题，但是将这两种情况混合起来，然后想:我对这个特定变量使用了什么情况，这是不太实际的。一致性很重要！
*   我们还遇到了引号和多余空格的问题。一些开发人员使用了`KEY=VALUE`而一些使用了`KEY = "VALUE"`，而事实上，这两个例子将解决完全相同的事情——我们更喜欢有一个——最好只有一个——显而易见的方法。所以，我们现在坚持使用`KEY=VALUE`符号
*   我们也曾经有一把复制的钥匙，毁了我的一天。[因为这个简单的问题，我花了几个小时](https://dev.to/sobolevn/i-am-a-mediocre-developer--30hn)调试我的应用程序。那对我来说是一个转折点，我已经决定:让我们自动化吧！

## 安装

您可以通过`pip`(或任何其他类似工具)安装:

```
$  pip install dotenv-linter 
```

Enter fullscreen mode Exit fullscreen mode

为什么是`pip`？因为`python`几乎出现在所有的 Linux 系统中。我们试图让这个工具尽可能的便携。

## 用法

用法真的很简单:

```
$  dotenv-linter path/to/your/.env even/multiple/files/are/fine/.env 
```

Enter fullscreen mode Exit fullscreen mode

## 现实生活中的例子

如果你对我们在现实生活中如何使用它感兴趣，你可以看看(甚至试试！)我们的 [`django`模板](https://github.com/wemake-services/wemake-django-template)。下面是调用它的[行](https://github.com/wemake-services/wemake-django-template/blob/master/%7B%7Bcookiecutter.project_name%7D%7D/docker/ci.sh#L55)。

在我们的文件中也有完整的[林挺规则列表，请查看。](https://dotenv-linter.readthedocs.io/en/latest/pages/violations/index.html)

## 结论

我希望这个简单的工具能为您节省一些时间，让您的项目更加一致，让您的生活稍微好一点。[在 github 上添加我](https://github.com/sobolevn)以了解我正在开发的工具！