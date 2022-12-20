# 使用 Perl6 和 Sparrow6 的 Azure 自动化

> 原文：<https://dev.to/melezhik/azure-automation-with-perl6-and-sparrow6-45eh>

Sparrow6 是一个基于 [Perl6](https://perl6.org) 编写的自动化工具，附带许多[插件](https://github.com/melezhik/sparrow-plugins)，允许用户自动化他们的日常任务。

在这篇短文中，我将向你展示如何自动化与 Azure KeyVault 的交互。这种情况很典型，因为当人们需要从 keyvault 中读取秘密并以某种方式使用它们时，这种情况经常发生。

# 安装备用行 6

`zef install https://github.com/melezhik/Sparrow6.git`

# 设置备用 6 仓库

`export SP6_REPO=http://repo.southcentralus.cloudapp.azure.com`

# 简单场景

让我们创建一个简单的场景，从 keyvault 加载登录名和密码，并使用它们 ssh 到 linux VM，然后为这台机器运行`uptime`命令。

我们将使用 [Tomtit](https://github.com/melezhik/tomtit) 任务运行器
来执行 Sparrow6 场景，因为 Tomtit 非常适合命令行应用程序:

`zef install https://github.com/melezhik/Tomtit.git`

现在让我们创建场景:

**。tom/ssh-uptime.pl6**

```
#!perl6

# Set Azure Account 
task-run "set az account", "azure-account-set" %(
  subscription  => "foo-bar-baz"
);

# Load keyvault secrets from Azure KeyVault
my %state = task-run "load login and password", "azure-kv-show", %(
  kv      => "Stash" # key vault name
  secret  => ( "password", "login" ) # secret names
)

my $host = "192.168.0.1";

# Run uptime for linux host, using loaded credentials
bash "ssh-pass -p {%state<password>} ssh -l {%state<login>} $host uptime" 
```

Enter fullscreen mode Exit fullscreen mode

# 运行场景

现在，在通过用于向 Azure 发出请求的服务主体进行身份验证后，我们可以运行场景:

`az login --service-principal -u $app_id --tenant $tenant_id -p $password`

`tom ssh-uptime`

* * *

感谢您的阅读。如果你觉得这篇文章有用，请告诉我，我可能会就同一主题写新的。