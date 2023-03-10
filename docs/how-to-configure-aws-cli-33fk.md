# 如何配置 AWS CLI

> 原文：<https://dev.to/andremare/how-to-configure-aws-cli-33fk>

[![AWS](img/34986da899bee8b0af8828a7d41d2964.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y2NV80uF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uppbwn206i5omrbi8boh.png)

这篇文章提供了一组关于如何在 macOS 或任何其他操作系统上配置 Amazon Web Services 命令行界面(AWS CLI)的命令。AWS CLI 是一个开源工具，使 AWS 平台的用户能够与 AWS 服务通信并向其发出命令。

### 要求

以下列表定义了配置 AWS CLI 工具所需的技术、工具和凭据:

*   AWS 帐户和凭证。
*   已安装 AWS CLI 工具。([MAC OS 上的 AWS CLI](https://www.code2bits.com/how-to-install-awscli-on-macos-using-homebrew/))
*   iTerm 或终端。([MAC OS 上的 ITER m2](https://www.code2bits.com/how-to-install-iterm2-on-macos-using-homebrew/))

### 什么是 AWS CLI？

AWS CLI 是一个开源工具，它使 AWS 平台用户能够与其服务进行通信并向其服务发出命令。命令行工具使用户能够通过不同的公共 API 调用 AWS 平台服务。通过使用用户的访问密钥和密码配置 AWS CLI，用户可以抽象出身份验证和其他安全机制。此外，它通过将 AWS 服务的细粒度公共 API 组合成简单的操作来提供更高级别的操作。对于用户来说，使用这些更高级的操作会更自然。用户现在可以通过创建 shell 脚本来管理他们的 AWS 服务，并自动执行简单和复杂的任务。

### 快速命令

本节提供了有关如何配置 AWS CLI 的命令。

```
$ aws configure
AWS Access Key ID [****************OMLQ] : ********
AWS Secret Access Key [****************BQWW] : ***********
Default region name [us-east-1]: eu-west-1
Default output format [json]: json 
```

Enter fullscreen mode Exit fullscreen mode

**默认区域**:可用区域列表见 [AWS 区域和端点](https://docs.aws.amazon.com/general/latest/gr/rande.html)。

**默认输出**:默认输出格式可以是 *json* 、 *text* 或 *table* 。

### AWS 配置文件

**~/。AWS/credentials**

AWS 凭证文件包含多个命名配置文件的凭证，包括默认配置文件。每个配置文件的凭证由 aws_access_key_id 和 aws_secret_access_key 属性的键/值组成。AWS 凭证文件位于~/。macOS 上的 AWS/凭证。

```
$ cat ~/.aws/credentials
[default]
aws_access_key_id=1234567890
aws_secret_access_key=ABCDEFGHIJKLMNOPQRSTUVWXYZ

[user2]
aws_access_key_id=0987654321
aws_secret_access_key=ZYXWVUTSRQPONMLKJIHGFEDCBA 
```

Enter fullscreen mode Exit fullscreen mode

**~/。AWS/config**

CLI 配置文件包含多个命名配置文件的配置，包括默认配置文件。每个配置文件的配置由区域和输出属性的键/值组成。AWS CLI 配置文件位于~/。macOS 上的 aws/config。

```
$ cat ~/.aws/config
[default]
region=us-west-2
output=json

[profile user2]
region=us-east-1
output=text 
```

Enter fullscreen mode Exit fullscreen mode

### AWS 配置命令

以下是在配置 AWS CLI 工具期间可以使用的其他命令。

### 1。AWS 配置命名的概要文件

您可以使用–profile 参数作为 AWS Configure 命令的一部分来配置命名的 pro 文件。

```
$ aws configure --profile administrator
AWS Access Key ID [None] : 12334567890
AWS Secret Access Key [None] : ABCDEFGHIJKLMNOPQRSTUVWXYZ
Default region name [None] : eu-west-1
Default output format [None] : json 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**命名配置文件参数–profile 可以附加到任何 AWS 配置命令中，以便在该命名配置文件的上下文中执行该命令。

### 2。更新特定属性

您可以多次运行 AWS Configure 命令来更新特定的属性。到现有值显示在[括号]中。要更新现有值，请键入新值并按 enter 键。要保留现有值，请在不键入任何值的情况下按 enter 键。

```
$ aws configure
AWS Access Key ID [****************1234]: 
AWS Secret Access Key [****************1234]: 
Default region name [eu-west-1] : 
Default output format [json] : table 
```

Enter fullscreen mode Exit fullscreen mode

### 3。列表配置数据

以下命令可用于列出默认配置文件的 AWS CLI 配置数据。对于每个配置项，它将显示值、配置值的检索位置以及配置变量名。

```
$ aws configure list
      Name                    Value             Type    Location
      ----                    -----             ----    --------
   profile                <not set>             None    None
access_key     ****************1234 shared-credentials-file    
secret_key     ****************1234 shared-credentials-file    
    region                eu-west-1      config-file    ~/.aws/config 
```

Enter fullscreen mode Exit fullscreen mode

### 总结

恭喜你！您已经成功配置了 AWS CLI，现在将能够向 AWS 平台发出命令。在任何不同的社交媒体平台上关注我，并随时留下评论。

帖子[如何配置 AWS CLI](https://www.code2bits.com/how-to-configure-aws-cli/) 最早出现在 [Code2Bits](https://www.code2bits.com) 上。