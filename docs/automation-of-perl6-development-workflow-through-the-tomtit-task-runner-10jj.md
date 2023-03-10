# 通过 Tomtit 任务运行器实现 Perl6 开发工作流的自动化

> 原文：<https://dev.to/melezhik/automation-of-perl6-development-workflow-through-the-tomtit-task-runner-10jj>

有一只小山雀能成功！

* * *

[![tomtit cli](img/861e6a4bff49c853db91133b26902853.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NN9nXNLz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hz2t22yyvuqrsbm9wkia.png)

Tomtit 是一个全新的 Perl6 任务运行器，允许您轻松地自动化日常任务。

在这篇文章中，我将展示如何使用 Tomtit 来自动化 Perl6 应用程序开发过程中的典型任务。

为了一瞥 Tomtit 是什么，请阅读这篇[帖子](https://dev.to/melezhik/one-tomtit-for-it--1j3f)。

# 虎视眈眈

先装 Tomtit 吧。

`zef install Tomtit`

现在让我们检查来自远程 git 存储库的应用程序源代码:

`git clone $git-repo`

# 4 例

让我们把这篇文章限制在开发和发布 Perl6 应用程序时经常发生的 4 种典型情况:

*   设置 git 存储库
*   测试本地源代码
*   安装本地源代码
*   释放 CPAN 模块

# Tomtit 场景

现在我们来为每一个提到的情况定义 tomtit 场景。我们将场景放在`.tom`目录中，这些是调用 Tomtit API 的普通 Perl6 脚本。

案例一。设置 git 存储库。

当在新机器上启动时，我总是必须设置我的用户/电子邮件，这样我就可以远程推送。我也希望 git 为我保留密码缓存。每次手动操作都很繁琐，所以让我们自动操作:

。tom/set-get.pl6:

```
task-run "set git", "git-base", %(
  email                => 'melezhik@gmail.com',
  name                 => 'Alexey Melezhik',
  config_scope         => 'local',
  set_credential_cache => 'on'
); 
```

我的工作流程让它变得有点复杂，因为我将源代码同时推送到 GitHub 和 BitBucket 存储库。

很容易为推送操作创建场景:

。tom/push-gh.pl6:

```
# push to github
bash "git push origin master"; 
```

。tom/push-bb.pl6:

```
# push to bitbucket
bash "git push bb master"; 
```

。tom/push.pl6:

```
# push to both
EVALFILE ".tom/push-bb.pl6"
EVALFILE ".tom/push-gh.pl6" 
```

案例二。测试本地源代码。

对于许多 Perl6 项目来说，在`t/`目录中运行单元测试就像`zef test`一样，但是我也有 Outthentic 测试。我还喜欢验证我的 META6.json 文件，看看其中是否有任何与 json 相关的错误:

。tom/test.pl6:

```
task-run "check json files", "json-lint", %( path =>  "{$*CWD}" );
bash "zef test .";
bash "sh run-test.sh"; 
```

案例三。安装源代码。

这只是当前工作目录的`zef install`命令。

。tom/install.pl6:

```
bash "zef install . --force-install"; 
```

案例 4。释放 CPAN 模块

我使用 [App::Mi6](https://modules.perl6.org/dist/App::Mi6:cpan:SKAJI) 工具将我的 Perl6 模块发布到 CPAN，这需要通过`~/.pause`设置凭证，为此定义一个专用场景似乎很方便:

。tom/set-pause.pl6:

```
my $password = prompt("enter your CPAN password: ");

template-create "/home/{%*ENV<USER>}/.pause", %(
  mode => '700',
  variables => %(
    user      => 'melezhik',
    password  => $password
  ),
  source => q:to /TEMPL/
  user      [%= user %]
  password  [%= password %]
  TEMPL
); 
```

发布场景简单如下:

。tom/release.pl6:

```
zef "App::Mi6";
bash "mi6 release"; 
```

# 工作流程

当我准备好我的 tomtit [场景](https://github.com/melezhik/tomtit/tree/master/.tom)时，我的开发工作流非常简单明了，当一切就绪时:

签出应用程序源代码:

`git clone $git-repo`

安装 Tomtit:

`zef install Tomtit`

列出可用的 tomtits 方案:

`tom --list`

修改源代码

`nano foo/bar/app.pl6`

运行测试:

`tom test`

安装:

`tom install`

Setup git repo:

`tom git-setup`

提交更改

`git commit -a`

推送更改:

`tom push`

设置暂停帐户凭据:

`tom set-pause`

释放到 CPAN:

`tom release`

# 结论

就是这样。在这篇文章中，我们看到了在开发 Perl6 应用程序时如何自动化一些典型的日常操作。

只有一只小山雀能成功！

* * *

感谢阅读