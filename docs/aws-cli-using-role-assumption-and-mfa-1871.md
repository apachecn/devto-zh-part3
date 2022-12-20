# 使用角色假设和 MFA 的 AWS Cli

> 原文：<https://dev.to/yloganathan/aws-cli-using-role-assumption-and-mfa-1871>

我想设置 cli 来访问启用了 MFA 的主 AWS 登录的不同角色。我发现文章设置 MFA 或做角色假设，但不是两者兼而有之。这是我试图将 MFA 和角色假设的一些解决方案结合起来。

*感谢斯蒂芬·哈里斯[https://github . com/SWE Harris/AWS-CLI-MFA/blob/master/get-AWS-creds](https://github.com/sweharris/aws-cli-mfa/blob/master/get-aws-creds)T3】*

## 多个 AWS 账户

拥有多个 AWS 帐户来处理组织的不同需求已经成为在 AWS 中工作的首选方法。为工具、开发、生产和试运行建立独立账户的一些优势。

*   资源分离。生产没有意外更新。
*   可以对每个帐户实施不同的资源限制。
*   计费变得更加简单明了。

## 访问多个账户:

有两种方法可以管理每个用户的多个帐户。

*   每个帐户单独登录，这是很多用户和登录管理。
*   一次登录一个主帐户，并使用一个名为`Role Assumption`的概念来访问使用角色的相关 AWS 帐户。

承担角色意味着 AWS 令牌服务将为您提供临时凭证，以便使用承担的角色访问帐户。您的主用户应该拥有正确的信任关系配置来承担所请求的角色。

*   [关于 AWS 角色承担的更多信息](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use.html)

## 通过 MFA 提高安全性

无论您使用一个 aws 帐户还是多个 aws 帐户并通过角色访问它们，AWS 都建议启用多因素身份验证以提高安全性。我们可以使用硬件、文本或虚拟 MFA 设备，如 google authenticator 应用程序，作为除登录凭据之外的第二层安全措施。

*   [启用 MFA 的 Aws 指南](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa_enable.html)

## 设置浏览器扩展以访问多个帐户

*   打开 chrome，使用凭据和 mfa 登录主帐户
*   安装 chrome 扩展 [AWS 扩展开关角色](https://chrome.google.com/webstore/detail/aws-extend-switch-roles/jpmkfafbacpgapdghgdpembnojdlgkdl)
*   安装完成后，点击扩展并选择**配置**来设置`~/.aws/config`格式的配置。

示例:

```
 [dev]
    aws_account_id = 210987654321
    role_name = Developer
    source_profile = default
    color = ffff00

    [uat]
    aws_account_id = 123456789012
    role_name = Developer
    source_profile = default
    color = ff00ff

    [prod]
    aws_account_id = 098765432132
    role_name = Developer
    source_profile = default
    color = ff0000 
```

*   刷新浏览器，现在我们可以在浏览器中切换帐户，只需点击左上角的帐户别名/号码。

## 通过 CLI 使用 MFA 访问多个帐户

让我们了解一下 aws cli 使用的文件。

*   `~/.aws/config` -有配置文件和配置文件的默认值
*   `~/.aws/configure` -每个配置文件都有 aws_access_key_id 和 aws_secret_access_key。Aws cli 检查环境，然后检查密钥和访问权限的配置文件。

### 设置默认用户

这两个文件都有一个默认条目，即您的主帐户登录密钥。

*   [配置默认用户。](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)

### 使用 MFA 基本配置文件的角色假设

我们将使用配置和凭证文件，通过角色假设来验证正确的角色。

*   将 [get-aws-creds.sh](https://gist.github.com/Yloganathan/c24a3d99213c72c7316269a1888b2600) 复制到`~/.aws/get-aws-creds.sh`
*   我们将使用我们用于浏览器的配置文件的一个稍微改变的版本。请注意，角色需要是 arn，我们将 source_profile 更改为`mfa`而不是`default`

    样本`~/.aws/config`文件

```
 [profile dev]
    region = us-east-1
    role_arn = arn:aws:iam::<account_number>:role/Developer
    source_profile = mfa

    [profile uat]
    region = us-east-1
    role_arn = arn:aws:iam::<account_number>:role/Developer
    source_profile = mfa

    [profile prod]
    region = us-east-1
    role_arn = arn:aws:iam::<account_number>:role/Developer
    source_profile = mfa 
```

*   设置别名`alias mfa=~/.aws/get-aws-creds.sh`

我们完了。现在我们可以使用命令`mfa`为默认用户设置令牌。

```
$ » mfa
You are: yloganathan
Your MFA device is: arn:aws:iam::--redacted--:mfa/yloganathan
Enter your MFA code now: 468114
Removing old mfa setting
Push new mfa token, key, id to credentials
AWS_SESSION_TOKEN=FQoGZXIvYXdzEMD//////////wEaDBYMaD/jCVsms7LcZCKwAb55t+hRUevOscgmhO1kPsFIRC2lUjZL9L3V2iSSo8pyUQay8GnvRw/PrMQp/XSJhoIkiOmnnq/+GH7Id7DhiTR0R2+tv+d6onYhOmSoLg2NnBbk6J038DHANz8JKsCrXIoSxsdfserfuVJrqECTSLdi+EsF1OnF+nZGCPxVJmWETWaQFZ4lYD2VqmMP68GuIzw4y1p9kpYQkkcEZ8jIh8dzS5m88OYTxNKJve4OYF
AWS_SECRET_ACCESS_KEY=yrhWWewKa63048GF84u/OPqweERAw+iyQcMYgXKF
AWS_ACCESS_KEY_ID=ASIAT7ADEFZVLJWNKRKFL
Keys valid until 2019-05-11T02:35:07Z 
```

我们可以使用`--profile`选项访问不同的 aws 账户。

```
$ » aws s3 ls
An error occurred (AccessDenied) when calling the ListBuckets operation: Access Denied

$ » aws s3 ls --profile dev
2019-04-08 13:17:00 cf-custom-resource-dev-somebucket-19qgbsjff4jw1
2019-02-01 15:04:28 cf-templates-1lguyp5t2xsbe-us-east-1
2019-02-28 16:13:41 cf-templates-1lguyp5t2xsbe-us-west-2 
```

我想听听你如何从 aws cli 使用 MFA 访问假定的角色。