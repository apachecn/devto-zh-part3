# AWS 跨帐户快速访问只需 3 分钟

> 原文：<https://dev.to/omerxx/aws-cross-account-quick-access-in-3-minutes-55a6>

你可以点击菜单上的一个按钮，快速切换到你的任何一个 AWS 账户，按名称和颜色列出它们。

想在几分钟内了解如何配置该列表吗？

使用多个帐户是许多组织的常见做法。
无论是开发环境整合、计费分离和粒度，还是第三方服务账户，使用多个账户都是日常需要。

作为一名顾问，我需要定期与其他内部客户以及客户共享我的客户资源。

有一段时间，我习惯于一天断开和重新连接几十次。当这种行为变得太频繁而无法忍受时，我必须找到一个合适的解决方案，一个能让我自由地从一个帐户跳到另一个帐户，而没有改变登录会话的麻烦的解决方案。

AWS 跨帐户访问角色是一种设置，在该设置中，给定帐户通过让另一个帐户承担预先配置的 IAM 角色来验证该帐户的访问权限。然后，外部帐户的用户和资源可以承担给定的角色，并在其 IAM 角色权限范围内使用新帐户的资源。

配置跨帐户访问角色需要几分钟时间，只需执行几个简单的步骤。配置结束时，您将能够从 AWS 控制台中始终可见的下拉菜单中选择“更改角色”,并快速跳转到具有交叉访问角色的帐户集:

在 AWS 控制台中，从 IAM 控制台的侧栏菜单中选择服务并搜索身份和访问管理
，选择角色
创建新角色并提供描述性名称
在选择角色类型处选择跨帐户访问的角色选项:

提供您允许访问的帐户的帐户 ID
[帐户 ID 可通过右上角的支持和支持中心找到，该 ID 将显示在支持链接下]
提供角色的策略模板，无论是 AWS 管理的还是自定义构建的策略
批准您的更改
现在:

单击 AWS 控制台右上角的用户名。然后从下拉列表中选择切换角色

提供帐户名、给定角色和颜色，以便将来从控制台菜单快速访问

选择快速识别和角色切换的颜色
从现在开始，只需点击一个按钮，已配置的账户就可以快速切换

啊，等等，我们都没见过彩色的控制台，对吧？UI 很烂。

你从你的控制台运行一切，对不对？:)

让我们看看如何使用 AWS CLI 轻松实现跨帐户角色的假设:

在 AWS CLI 配置文件
上创建新的 AWS 概要文件

```
[ Normally found under ~/.aws/credentials ]
$ cat ~/.aws/credentials
[profile crossaccountrole]
role_arn = arn:aws:iam::123456789012:role/xaccount
source_profile = default 
```

Enter fullscreen mode Exit fullscreen mode

一旦您的新跨帐户配置文件设置完毕，您就可以使用配置文件标志
使用任何 CLI 行

```
$ aws s3 ls --profile crossaccountrole 
```

Enter fullscreen mode Exit fullscreen mode

另一个选项是设置一个环境变量`AWS_PROFILE`，它将被任何 AWS SDK 或 CLI
自动使用

```
$ export AWS_PROFILE=crossaccountrole 
```

Enter fullscreen mode Exit fullscreen mode

*到了！*

您不仅可以在控制台中对所有帐户进行颜色编码并准备好快速切换，还可以对所有 CLI 帐户使用单一秘密凭据。