# 使用 Terraform 创建或删除 GitHub Repos

> 原文：<https://dev.to/joehobot/creating-or-deleting-github-repos-with-terraform-5g2>

刚刚在 twitter 上看到一些关于帮助您删除多个 github 的网站的推文。虽然这一切都很好，但我真的不相信让其他网站访问我的组织或任何类型的 API，甚至是我的个人 github 帐户。

现在请记住，要使用 terraform github provider，您需要有一个 org，否则它不能与普通用户一起工作。

我一直在使用 terraform 进行几乎所有的基础设施编排，包括 GitHub 权限、回购、用户、组织等等...因为管理回购协议和执行某种带有制衡的审计可能会变得混乱。

这里有一个超级简单的方法来管理你的 GitHub 回购。我不会深入设置变量，但是你可以查看 GitHub 提供者的更多信息。

首先你需要一个[令牌](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)，然后创建一个提供者，所以创建文件 provider.tf

```
provider "github" {
  token        = "YourTokenGoesHere"
  organization = "YourOrg"
} 
```

接下来，创建一个文件来保存你的 repos 和合作者。

```
resource "github_repository" "mynewrepo-repo" {
  name        = "mynewrepo-repo"
  description = "Sweet Terraform Repo Yey..."
}

resource "github_repository_collaborator" "a_repo_collaborator" {
  repository = "mynewrepo-repo"
  username   = "wow-much-love$$"
  permission = "admin"
} 
```

运行这些命令...

首先你需要初始化 terraform，这样你就可以得到模块等。

```
terraform init 
```

完成初始化后，运行一个 terraform 计划，看看在应用更改之前 terraform 会为您做些什么。

```
terraform plan 
```

应该是这样的

```
 + github_repository.mynewrepo-repo
      id:                 <computed>
      allow_merge_commit: "true"
      allow_rebase_merge: "true"
      allow_squash_merge: "true"
      archived:           "false"
      default_branch:     <computed>
      description:        "Sweet Terraform Repo Yey..."
      etag:               <computed>
      full_name:          <computed>
      git_clone_url:      <computed>
      html_url:           <computed>
      http_clone_url:     <computed>
      name:               "mynewrepo-repo"
      ssh_clone_url:      <computed>
      svn_url:            <computed>

  + github_repository_collaborator.a_repo_collaborator
      id:                 <computed>
      permission:         "admin"
      repository:         "mynewrepo-repo"
      username:           "wow-much-love$$"
Plan: 2 to add, 0 to change, 0 to destroy. 
```

如果你对上述结果感到满意，并且你没有任何类似毁灭或改变的东西。随意跑

```
terraform apply 
```

几秒钟之内，您应该会在您的组织中看到您的新回购。

现在，这是创建回购和添加合作者的超级肮脏和快速的方式。

然而，在现实世界中，你会创建[团队](https://www.terraform.io/docs/providers/github/r/team.html)，你会将用户添加到那个[团队成员](https://www.terraform.io/docs/providers/github/r/team_membership.html)，然后你会将[团队添加到存储库](https://www.terraform.io/docs/providers/github/r/team_repository.html)，如果你已经有了回购，你肯定会首先[导入它们](https://www.terraform.io/docs/providers/github/r/repository.html#import)。

哦，要删除您通过 terraform 创建的 repo，只需从上面的步骤中删除 repo 和 collaborator，就可以了。

如前所述，创建 github repos 真的很快。设置这一切不会花费你超过 15 分钟的时间。