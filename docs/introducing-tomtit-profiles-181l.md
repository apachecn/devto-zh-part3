# Tomtit 简介

> 原文：<https://dev.to/melezhik/introducing-tomtit-profiles-181l>

# Tomtit 简介

[![bird](img/ec523a7746dfb765444f382bd16b8d2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3SF0n7PP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.goodfreephotos.com/albums/vector-images/red-bird-vector.png)

照片 via [好的免费照片](https://www.goodfreephotos.com)

有了最新版本的 Tomtit ,你可以开始使用配置文件——预定义的任务集。您可以安装为一些常用类别定义的脚本，而不是从头开始编写代码。

# 类别

要列出可用的类别，运行`tom --profile`，你会得到列表:

*   Azure Devops 的 ado //助手脚本
*   Git//Git 的助手脚本
*   hello //示例助手脚本
*   perl //用于 perl5 开发的助手脚本
*   perl6 //用于 perl6 开发的助手脚本
*   Ruby//Ruby 的助手脚本(目前只是 rvm 安装程序)

一旦选择了想要的类别——只需将类别脚本部署到您的 tomcat 缓存中:

```
git clone $project
tom --profile git
tom --profile perl6 
```

一旦配置文件被执行，它将安装所有的脚本:

```
tom --profile git
install git@commit ...
install git@pull ...
install git@push ...
install git@set-git ...
install git@status ... 
```

当引导一个新项目时，它可以节省你的时间。许多脚本可以直接使用，有些需要稍加调整。这里我们需要做一点小小的修改来设置我们的本地 git 存储库，用我们的个人数据替换存根:

汤姆/机甲猎人第六季第 0 集

```
#!perl6

task-run "set git", "git-base", %(
  email => 'user@email.com',
  name  => 'User Name',
  config_scope => 'local',
  set_credential_cache => 'on'
); 
```

在这里，我们准备使用新安装的脚本:

*   tom set-git //要设置 git 储存库，应该运行一次
*   tom commit //向 git 提交更改
*   tom push //将更改推送到位

与我们在 Perl6 开发工作流中使用助手的方式相同:

```
tom --profile perl6 
install perl6@install ...
install perl6@release ...
install perl6@set-pause ...
install perl6@test ... 
```

现在:

*   tom test //运行 zef 测试
*   tom install //运行 zef install
*   汤姆释放//让 CPAN 释放

诸如此类。

# 定制

如果出于某些原因，您需要定制已安装的助手脚本，只需编辑它们，它们是普通的 Perl6 代码:

。tom/test.pl6:

```
#!perl6

task-run "check json files", "json-lint", %( path =>  "{$*CWD}" );
bash "zef test .";
bash "strun --root examples/"; # run Outthentic tests 
```

* * *

# 结论

Tomtit 概要文件最大限度地减少了引导新项目开发所需的时间，为您提供了有用的助手脚本来自动执行常用场景的典型任务。

试试吧！