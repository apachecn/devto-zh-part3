# aws-creds:介绍 aws 凭证选择器

> 原文：<https://dev.to/hammotime/aws-creds-introducing-aws-credential-picker-5gbo>

*本文交叉转贴自 [hammo.io](https://www.hammo.io/2019/06/04/aws-creds-credential-picker/) 。*

AWS 中的身份和访问管理非常简单。如此简单，事实上，大多数人没有考虑他们的帐户的架构或他们将如何管理权限，直到为时已晚。我去过那里，我也这样做过。在一家快速创建帐户和配置 IAM 的企业中工作，最痛苦的事情之一就是管理您所有的登录。当你自动化和使用你的访问键时，这变得更糟。都是为了让这个过程变得更简单。
[![](img/be23858173775ba5a22cbf4cb855d0bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--61HYa_8h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mfr9fq1766262cbh7g15.png)

通常，在一个架构良好的环境中，您将混合使用 AWS SSO 或某种联合凭证系统，并且您将使用角色委托来为您的用户提供访问权限。但是，如果您没有这些可用的资源，并且您有很多帐户，该怎么办呢？输入 aws-creds (GitHub)。`aws-creds`旨在简化多个互不关联的账户的管理。不用更新您的`~/.aws/credentials`文件中的概要文件，您有一个命令行工具将您的所有概要文件保存在一个定制的 json 文件中。

# 它是如何工作的？

AWS 凭证选择器的设计非常简单。我们没有将详细信息存储在凭证文件中——这对于大量概要文件来说很难使用——而是将它们存储在一个定制的 JSON 文件中。当您`login`时，您实际上是在`~/.aws`中创建凭证和配置文件。当你`logout`的时候，这些文件正在被删除。

这样做的原因是一些 AWS 工具和第三方工具不喜欢您有多个概要文件，并且没有正确处理环境变量。通过始终拥有单个凭证集，实用程序将始终工作。

该脚本还提供了访问密钥的更新信息，并建议您何时轮换它们。

# 入门

开始使用`aws-creds`很容易。我已经把它打包成一个 PyPi 包，可以通过 pip 获得。要安装`aws-creds`，只需运行以下命令:

```
python3 -m pip install aws-creds 
```

要确保安装成功，请运行以下命令:

```
aws-creds version 
```

# 您想了解更多吗？

检查一下 [GitHub 库](https://github.com/HammoTime/aws-creds)和 [PyPi 包](https://pypi.org/project/aws-creds/)。如果你发现任何错误或者有功能需求，请在 [GitHub 问题](https://github.com/HammoTime/aws-creds/issues)中告诉我。感谢您的阅读，下一篇文章再见。如果你想要一些与 AWS 相关的文章，请参阅我的关于启动和运行 terra form 的文章。