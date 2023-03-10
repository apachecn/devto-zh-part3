# 用 Tomtit 实现 AzureDevops 工作流的自动化

> 原文：<https://dev.to/melezhik/automation-of-azuredevops-workflow-with-tomtit-1ec2>

[![tomtit logo](img/4decc28d4321d096b0a1534d39f4bb00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZfMjxIkT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/melezhik/tomtit/master/tt-base.png)

* * *

我的主要任务之一是使用 VSTS / Azure 维护和开发构建。这些构建主要是为了通过 Azure 资源创建基础设施。在这篇文章中，我将展示如何让我的工作流程变得更简单，让我通过命令行界面管理我的所有任务。

# 项目资源库

我典型的 VSTS 构建库包含描述“管道”构建逻辑(*)的`azure-pipelines.yml`。

对我来说，这只是意味着，构建逻辑是在`azure-pipelines.yml`文件中定义的，我只在其他**项目相关文件**中进行更正，提交那些更改，将它们推回并启动新的构建。

因此，首先我需要克隆我正在工作的库:

```
git clone $vsts-build-repo 
```

Enter fullscreen mode Exit fullscreen mode

* * *

(*)你可以在微软[文档页面](https://docs.microsoft.com/en-us/azure/devops/pipelines/get-started-yaml?view=vsts)的 AzureDevops Pipelines 上了解更多信息。

# 管理 VSTS 建

最近微软推出了方便的命令行界面来与 Azure Devops 交互，它被称为 [vsts cli](https://docs.microsoft.com/en-us/cli/vsts/overview?view=vsts-cli-latest) ，在我的情况下，只是因为我积极地使用 Tomtit 来管理我的工作流，我创建了一个麻雀[插件](https://sparrowhub.org/info/vsts-build)来包装`vsts cli`，并向现有的 vsts cli 工具添加一些额外的逻辑。

因此，我在处理 VSTS 构建时的典型工作流由两个任务组成:

*   运行生成
*   查看构建状态

让我们来看看这是如何通过 Tomtit 自动完成的。

# Tomtit 阿多简介

首先，我需要安装 Tomtit runner 附带的 ado 配置文件，这个配置文件包含了我们在使用 AzureDevops 时需要的所有脚本。

```
tom --profile ado

install ado@az-account-set ...
install ado@az-kv-show ...
install ado@az-resources ...
install ado@build ...
install ado@list ... 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们对这两个感兴趣:

*   `ado@build`
*   `ado@list`

如果我们看一下脚本的内容，就会发现可以根据我们的需求轻松定制的存根:

`tom --cat --lines build` :

```
[scenario build]

[1] task-run "run build", "vsts-build", %(
[2]   definition => 'change me'
[3] ); 
```

Enter fullscreen mode Exit fullscreen mode

我们只需要更改第二行来设置 VSTS 构建定义名称，以运行构建:

`tom --edit build` :

```
 task-run "run build", "vsts-build", %(
  definition => 'Build01'
); 
```

Enter fullscreen mode Exit fullscreen mode

ado@build 脚本所做的只是针对当前 git 分支运行 vsts 构建，您已经克隆了存储库。

下一个脚本`ado@build`确实需要定制，它列出了我们从中克隆库的 VSTS 项目的最后`N`构建。

`tom --cat list` :

```
[scenario list]
task-run "list builds", "vsts-build", %(
  action => "list"
) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我所有的工作流程都归结为那些典型的操作:

*   编辑文件

*   提交我的更改

*   推送我的更改

*   运行 vsts 构建

*   跟踪最后 N 个生成以查看生成状态

同样，当我使用 Tomtit 作为工作流管理时，它为我提供了很好的助手来简化与 git 相关的操作:

`tom --profile git`

```
install git@commit ...
install git@git-summary ...
install git@pull ...
install git@push ...
install git@set-git ...
install git@status ... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经做好了一切准备:

*   编辑文件=> `nano azure-pipelines.yml`

*   提交更改=> `tom commit`

*   推送修改=> `tom push`

*   运行构建=> `tom build`

*   列出构建状态=> `tom status`

我工作流程的另一个重要部分是测试创建的 Azure 资源。

# 查看 Azure 资源

典型地，每个 VSTS 构建都创建特定的 Azure 资源，检查这些资源被证明是非常重要的。

这可以归结为 3 个典型任务:

*   设置当前 azure 帐户。因为我可以在不同的订阅中工作，有时需要更改帐户。

*   列出给定资源组的资源

*   显示密钥存储库机密

所有这些任务都包含在现有的`ado@`脚本中:

*   `ado@az-account-set` -设置 az cli 活期账户

*   `ado@az-resources` -列出资源

*   `ado@az-kv-show` -打印出保险库密钥

当然，有些脚本需要定制:

设置帐户

`tom --cat --lines az-account-set` :

```
[scenario az-account-set]
[1] #!perl6
[2]
[3] task-run "set az account", "azure-account-set", %(
[4]   subscription => "change me"
[5] );
[6] 
```

Enter fullscreen mode Exit fullscreen mode

我们将更改第 4 行，以从配置文件中传递订阅 id:

`tom --edit az-account-set` :

```
#!perl6

task-run "set az cli account to {config<subscription><name>}", "azure-account-set", %(
  subscription => config<subscription><id>
) 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们创建默认配置:

`tom --env-edit default` :

```
{
  subscription => %(
    id => "265a768e-8c76-44d3-sa7b-03a19b756678",
    name => "DevSubscription"
  ),
  group => "Grp01",
  keyvault => "KVTest"  
} 
```

Enter fullscreen mode Exit fullscreen mode

每当我们需要在订阅之间切换时，我们只需要创建一个新环境并切换到它:

```
tom --env-edit production
tom --env-set production 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，我们将使用`default`环境。

现在让我们定制`ado@az-resources`脚本:

`tom --cat --lines az-resources` :

```
[1] #!perl6
[2]
[3] task-run "resources list", "azure-resource-list", %(
[4]   group     => "changeme",
[5]   pattern  => "changeme"
[6] );
[7] 
```

Enter fullscreen mode Exit fullscreen mode

我们更改第 4 行来通过配置文件传递`resource group`参数，并更改第 5 行来添加额外的资源过滤器:

`tom --edit az-resources` :

```
#!perl6

task-run "{config<group>} resources", "azure-resource-list", %(
   group     => config<group>,
   pattern   => "abc"
); 
```

Enter fullscreen mode Exit fullscreen mode

我们对`ado@az-kv-show`脚本做了很多相同的事情，它被证明非常方便，因为我经常需要在不同的订阅/项目之间切换时看到**密钥库秘密**:

`tom --cat --lines az-kv-show`

```
[scenario az-kv-show]
[1] #!perl6
[2]
[3] task-run "keyvault secret", "azure-kv-show", %(
[4]   kv      => 'changme', # key vault name
[5]   secret  => 'changme'  # key vault secret
[6] );
[7] 
```

Enter fullscreen mode Exit fullscreen mode

我们正在更改第 4 行和第 5 行，以在机密列表中设置实际的密钥库名称:

`tom --edit az-kv-show` :

```
#!perl6

task-run "keyvault secret", "azure-kv-show", %(
  kv      config<keyvault> , # key vault name
  secret  => ( "client_id", "client_secret", "db_login", "db_password") 
); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经准备好通过 Tomtit 管理一切:

*   `tom az-account-set` = >设置 az cli 账户
*   `tom az-resources` = >列出资源
*   `tom az-kv-show` = >打印出秘密

这些命令节省了我很多时间，并使新项目引导超级快速和容易。每次我开始使用新的构建/存储库时，我都会做同样的事情:

*   `tom --profile git` = >安装 git 帮助程序
*   `tom --profile ado` = >安装 AzureDevops 助手
*   `tom --edit` = >定制帮助
*   `tom --env-edit / --env-set` = >创建/设置环境/配置

# 结论

说到底，用 Tomtit 进行工作流管理是非常容易和有趣的！它最大限度地减少了重复步骤，并为您的项目创建了有争议的工具。

* * *

感谢您的阅读。