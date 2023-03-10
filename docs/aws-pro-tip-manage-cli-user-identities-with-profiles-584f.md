# AWS 专业提示:使用配置文件管理 CLI 用户身份

> 原文：<https://dev.to/letmypeoplecode/aws-pro-tip-manage-cli-user-identities-with-profiles-584f>

如果你使用 AWS 做很多事情，你会发现自己使用两件事…多个 IAM 用户和 AWS CLI。在认证培训课程(第三方)中，我经常看到讲师在每次为演示创建新用户时都使用新凭据重新配置 CLI。

这很好，但是您只能查看一次您的凭证，如果您没有将它们存储在某个地方，则必须生成新的凭证。将它们存储在 CLI 配置中是很好的……除非您用您创建的另一个 IAM 用户的凭证来攻击它们。

但是 AWS CLI 有带`profile`参数的用户管理特性。

添加新概要文件最简单的方法是在发出`configure`命令时简单地添加一个`profile`参数。

```
aws configure --profile myUserName 
```

Enter fullscreen mode Exit fullscreen mode

您可以将您为 IAM 用户指定的任何名称替换为 myUserName，将使用该用户名创建一个配置文件，存储用户的凭据和首选区域。然后，以该用户的身份在 CLI 中发出命令，就像创建一个 S3 存储桶……

```
aws s3 mb s3://bucket-name --profile myUserName 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我不想在每个命令上都指定概要文件呢？

首先，如果在配置过程中没有指定配置文件参数，AWS CLI 将在名为“default”的用户下存储凭据和区域。因此，您配置的没有名称的用户将是您不指定名称时使用的用户…除非…

AWS CLI 查找名为`AWS_DEFAULT_PROFILE`的环境变量。如果没有设置，默认配置文件是“default”，但是如果设置了，CLI 将使用它指定的任何配置文件名称。

因此，假设您刚刚创建了一个 mySysOp 用户，该用户将是您所有与 CodeBuild 和 CodeDeploy 相关的 CI/CD 命令的默认用户。您打开一个终端窗口(或 DOS 提示符，或 PowerShell)，您可以运行以下命令之一来为该 Shell 会话设置用户…

**Windows:**`set AWS_DEFAULT_PROFILE mySysOp`

**Linux/Mac**`export AWS_DEFAULT_PROFILE=mySysOp`

为了使它更持久(所有后续的终端都打开了，但是除了当前打开的终端之外，没有任何*)，您可以在 Windows 中使用`setx`，或者为 Bash shell 用户将该值添加到您的`.profile`文件中。*

我真的考虑过通过 Docker 启动虚拟机，并为每个 IAM 用户远程处理虚拟机，以避免 CLI 混乱，所以我真的很高兴我做了一些调查并了解了配置文件。如需全面了解个人资料，请访问关于个人资料的 [AWS 文档页面。](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)