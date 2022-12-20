# GitLab:你在开玩笑吧？

> 原文：<https://dev.to/ferricoxide/gitlab-youre-kidding-me-right-4i9>

我工作的一些组织运行他们自己的内部/私有 git 服务器(大多数是 GitLab CE 或 EE，但偶尔也有 GitHub EE)。然而，我们试图构建合同的方式是，我们维护我们生产的代码的总体所有权。作为其中的一部分，我们在 GitHub.Com 公司账户中完成所有的*发展。当客户需要他们在*的* git 服务器上的内容时，我们设置一个复制任务来处理必要的繁重工作。*

通过这种方式进行外部开发的副作用之一是，内部/私有 git 服务不会真正知道与外部提交相关的电子邮件地址。虽然您*可以*在内部/私有 git 服务中将您的所有外部电子邮件地址添加到您的帐户中，但其中一些外部电子邮件地址可能无法验证(例如，如果您使用 GitHub 的“noreply”地址隐藏选项)。

GitLab 使得在你的提交历史中有这些不可验证的地址不是特别有趣/容易解决。要“修复”这个问题，您需要进入 GitLab 服务器的管理 CLI 并进行修复。因此，要添加我的 GitHub“no reply”电子邮件，我需要做:

1.  GitLab 服务器的 SSH
2.  将权限(sudo)更改为能够调用管理 CLI 的帐户
3.  启动 GitLab 管理 CLI
4.  使用查询为目标帐户(我的投稿人帐户)设置修改句柄
5.  添加新的电子邮件地址(GitHub“no reply”地址)
6.  告诉 GitLab“你不需要验证这个”(强制:必须用欧比万·克诺比的声音说)
7.  点击保存并退出管理 CLI

对我来说，这基本上是这样的:

> ```
> gitlab-rails console production
> -------------------------------------------------------------------------------------
>  GitLab:       11.6.5 (237bddc)
>  GitLab Shell: 8.4.3
>  postgresql:   9.6.10
> -------------------------------------------------------------------------------------
> Loading production environment (Rails 5.0.7)
> irb(main):002:0> user = User.find\_by(email: 'my@ldap.email.address')
> => #<user id:532="" thjones2=""><br>irb(main):003:0&gt; user.email = 'ferricoxide@users.noreply.github.com'<br>=&gt; "ferricoxide@users.noreply.github.com"<br>irb(main):004:0&gt; user.skip_reconfirmation!<br>=&gt; true<br>irb(main):005:0&gt; user.save!<br>=&gt; true<br>irb(main):006:0&gt;<br></user> 
> ```

完成后，当我查看我的个人资料页面时，我的 GitHub“no reply”地址显示为已验证(所有与该地址相关的提交都显示在我的头像中)