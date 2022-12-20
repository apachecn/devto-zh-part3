# 在 AWS 代码提交上保护您的 Git 分支

> 原文：<https://dev.to/jorotenev/protecting-your-git-branches-on-aws-codecommit-4kol>

[![](img/3f43a1663b5364af006b30ebd68e6278.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---hkx5-RU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zg9g4s97p4ryysl0ptgz.jpg) 
*博罗韦茨，保加利亚里拉山*

* * *

这篇文章给出了一个快速的方法，告诉你如何只允许被选中的用户合并/提交到 CodeCommit Git 分支。

# 问题

让我们考虑一个常见的 CD/CI 场景:
1)您有一个团队，该团队将重要代码推送到 Git repo。
2)当准备好进行部署时，有人将`dev`合并到`master`
3)合并事件触发预配置的部署管道，该管道将打包来自回购的代码，然后将其交付生产

以上是非常好的，但是当涉及到谁应该被允许合并到生产分支时，必须小心。

# 解

([直接跳到示例 IAM 策略](#show-me-the-code))

因为我们是在 AWS 的环境中，所以解决方案在于使用 IAM 服务(身份和访问管理)。

下面是解决方案的主要步骤:

*   为所有开发人员建立一个通用小组，比如说`Dev-Group`。您团队中的所有开发人员都属于这个组。允许通用的代码提交操作，例如`pull`、`clone`等。
*   还要给`Dev-Group`附加一个策略，指定我们希望通过 [NotResource](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_notresource.html) 保护的存储库。对于所有其他回购开发商可以完全进入，如果需要的话。
*   有第二个 IAM 组-例如`ImportantRepo-PowerUser` -它有一个策略，为`ImportantRepo`回购启用`merge` / `commit`操作。
*   将选定的几个应该能够触发构建的开发人员添加到这个`ImportantRepo-PowerUser`组中

为什么要写这篇文章呢——好吧，如何实现上面的内容并不是那么明显。棘手的是 IAM 的[求值逻辑。由于这个原因，下面的天真的解决方案是行不通的:](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html#policy-eval-denyallow)

*   在`Dev-Group`中的所有开发，在`SelectedFew`中选择的几个
*   在`Dev-Group`的策略中使用`Deny`语句来拒绝设备合并到主设备
*   给`SelectedFew`附加一个策略，允许其用户合并到`master`

如果在`Dev-Group`中，你添加了一个声明，明确地**禁止**(带有`Deny`)该组的用户合并到 master 中，那么在不同的策略中就不可能允许这些动作——所以就不可能允许我们选择的几个开发者合并。

这就是 [`NotResource`](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_notresource.htmlhttps://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_notresource.html) 派上用场的地方。使用它，您可以从给定的语句**中排除受保护的存储库，而无需显式使用`Deny`动作**。也就是说，除了受保护的存储库之外，您可以对所有存储库授予完全权限。因为我们还没有在受保护的存储库上显式地拒绝 ied 动作，所以我们可以稍后在不同的策略中添加显式的允许——在附加到`SelectedFew`组的策略中。

## 显示代码

### 开发-组-通用-策略

我们需要 Dev-Group，它为所有的 Dev 提供通用的权限，并列出受保护的库。

```
// the policy atached to the Dev-Group
{
  "Version": "2012-10-17",
  "Statement": [
    // generic permissions for all devs
    {
      "Effect": "Allow",
      "Action": [
        "codecommit:Get*",
        "codecommit:GitPull",
        <whatever you need but not Push/Merge/etc.>
      ],
      "Resource": "*"
    },

    // the gotcha - in the NotResource put the important repos whose branches you want to protect
    {
      "Effect": "Allow",
      "Action": [
        "codecommit:GitPush",
        "codecommit:DeleteBranch",
        "codecommit:Merge*"
      ],
      "NotResource": [
        "arn:aws:codecommit:*:*:<important-repo-name-1>",
        "arn:aws:codecommit:*:*:<important-repo-name-2>"
      ]
    },

    // still allow all devs to push to non-production branches of the imporant repos
    {
      "Effect": "Allow",
      "Action": [
        "codecommit:GitPush",
        "codecommit:DeleteBranch",
        "codecommit:Merge*"
      ],
      "Resource": [
        "arn:aws:codecommit:*:*:<important-repo-name-1",
        "arn:aws:codecommit:*:*:<important-repo-name-2"
      ],
      "Condition": {
        "StringNotEquals": {
          "codecommit:References": [
            "refs/heads/master",
            "refs/heads/prod",
            "refs/heads/Stg"
          ]
        }
      }
    }
  ]
} 
```

### 重要-回购-1-电力-用户

某一特定重要回购的高级用户群。

```
// the policy attached to the group for users that can push to all branches for a given important repo 
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "codecommit:GitPush",
        "codecommit:DeleteBranch",
        "codecommit:Merge*"
      ],
      "Resource": [
        "arn:aws:codecommit:*:*:<important-repo-name-1>",
      ]
    }
  ]
} 
```

为了使解决方案更加通用，您可以对存储库使用一些命名约定——即所有以`xyz-`开头的回购名称都被认为是重要的，只有组`xyz-power-users`中的用户可以合并到这些回购的 prod 分支中。