# 使用 Bref、AWS SAM 和 Travis CI 将 PHP 应用程序自动部署到 AWS Lambda

> 原文：<https://dev.to/guillermoandrae/automate-deployment-of-php-applications-to-aws-lambda-with-bref-aws-sam-and-travis-ci-179>

如果你是一名 PHP 开发人员，而你选择的云提供商是 AWS，那么由于 AWS Lambda 缺乏对 PHP 的支持，你很可能经历过一点无服务器 FOMO。但是感谢 [AWS 无服务器应用模型](https://aws.amazon.com/serverless/sam/) (AWS SAM)和[Bref](https://bref.sh)——这两个开源项目都可以在 [GitHub](https://github.com) 上获得——现在可以将你的 PHP 应用部署到 Lambda，而不必摆弄 Node.js 垫片。在 Travis CI 的帮助下，运行自动化测试和部署基本上是轻而易举的事情。然而，还是有一些问题。

### 我不知道“Bref”是谁或什么

我也不知道，直到几周前。Matthieu Napoli 的“Bref”(法语中“brief”的意思)已经出现了一段时间，并引起了一些[知名 PHP-ers](https://akrabat.com/php-architect-serverless-php-with-bref-part-1/) 的注意。简而言之:

> Bref 提供了工具和文档来轻松部署和运行无服务器 PHP 应用程序。

在您继续熟悉项目的目标之前，您应该看一下 [Bref 文档](https://bref.sh/docs),浏览“入门”步骤，并阅读关于项目使用的建议(例如，不要期望使用 Bref 来部署您的遗留应用程序而不经历您通常与部署遗留应用程序相关联的那种困难)。

### 恭敬应有

这篇文章中详述的工作舒适地落在无私的巨人肩上；如果我不告诉你这篇文章解释了使用 AWS SAM 和 Travis CI 将 Python 应用程序部署到 AWS Lambda 需要什么，那我就是失职了。你会发现那里涵盖的很多内容在这里都派上了用场，作者([迈克·范布斯柯克](https://sysengcooking.com))在涵盖[相关先决条件](https://www.sysengcooking.com/posts/deploy-aws-lambda-functions-with-aws-sam-cli-and-travis-ci-1/)方面做得很好。事实上，我认为这个系列是这篇文章的先决条件，并建议你在继续之前阅读它，除非你没有耐心，只想得到好东西。

### 所以……特拉维斯 CI？

好吧。加入战斗。我已经在[https://github.com/guillermoandrae/bref-hello-world](https://github.com/guillermoandrae/bref-hello-world)建立了一个示例应用程序，并将使用它的. travis.yml 文件来解释这个过程:

```
language: generic
dist: xenial
before\_install:
  - phpenv global 7.2
install:
  - pip install --user awscli
  - pip install --user aws-sam-cli
script:
  - composer install --optimize-autoloader
  - composer test
  - composer package
deploy:
  provider: script
  script: composer deploy
  skip\_cleanup: true
  on:
    repo: guillermoandrae/bref-hello-world
env:
  global:
  - AWS\_DEFAULT\_REGION=us-east-1
  - secure: <obfuscated AWS Access Key ID>
  - secure: <obfuscated AWS Secret Key> 
```

### 最大的问题:构建环境

让我们从 tippy top 开始:

```
language: generic
dist: xenial 
```

粗略地看一下配置文件会发现两点:

*   我用的是 [phpenv](https://github.com/phpenv/phpenv) ，这是一个 PHP 版本管理器。
*   我使用的是 [pip](https://pip.pypa.io/en/stable/) ，这是 Python 的标准包管理器。

为了成功执行所有必要的任务，构建环境需要安装 PHP 和 Python。如果您以前在 php 项目中使用过 Travis CI，您可能已经在 YAML 文件的顶部用类似 language: php 的行指定了语言，这将提示 Travis CI 使用 PHP 构建环境。我需要使用一种不太为人所知的语言来支持我的用例。

Travis CI 提供了两个非特定于语言的构建环境:最小环境(T0)和通用环境(T2)。前者，顾名思义，相当光秃秃的。除了 gcc、curl 和一些其他工具，除了 Docker 和 Python，minimal 没有配备太多。另一方面，通用环境包括最小环境中包含的所有内容，以及一些额外的运行时，如 Go、Ruby 和 PHP。

如果您不熟悉它，Travis CI 配置文件的 dist 参数用于表示您想要使用的构建环境的特定发行版。使用 Ubuntu images，您有三个选项——trusty(默认，在没有指定发行版时使用)、precise 和 xenial——它们都包含特定的配置设置。然而，在最小和通用构建环境中，只有 precise 和 xenial 可用。我使用 xenial 环境，因为它包含合适的 PHP 版本。

### 关于 PHP 版本的更多信息

默认情况下，xenial 环境使用 PHP 三个版本中最低的版本:5.6。为了改变这一点，我使用 phpenv 来指定满足我们需求的版本。phpenv 安装在所有运行 PHP 的 Travis CI 映像上:

```
before\_install:
- phpenv global 7.2 
```

### CLI 工具安装与 Python 版本

在 Mike 的系列文章中，AWS CLI 和 AWS SAM CLI 工具的安装似乎非常简单，我在笔记本电脑上安装它们没有任何问题。然而，我发现自己在使用 Travis CI:
时遇到了这个错误

```
InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/security.html#insecureplatformwarning. 
```

我尝试使用 python 参数，然后是 TRAVIS_PYTHON_VERSION 环境变量来指定一个较新的 PYTHON 版本，但都没有给我带来快乐。因此，经过一番搜索，我找到了一个解决方案，在安装命令中添加- user 标志。所以最后我用了:

```
install:
- pip install --user awscli
- pip install --user aws-sam-cli 
```

### 作曲剧本 FTW

我真的，真的，真的，真的，真的很喜欢[作曲剧本](https://getcomposer.org/doc/articles/scripts.md)。过去，我使用 [Phing](https://www.phing.info/) 作为任务运行器，却没有意识到我可以利用 Composer 的脚本特性来做几乎完全相同的事情。我已经看到了众所周知的光明，并没有回头:

```
script:
- composer install --optimize-autoloader
- composer test
- composer package 
```

看一下该项目的 composer.json 文件的摘录，就能了解到底发生了什么: