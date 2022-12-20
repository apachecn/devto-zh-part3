# 如何使用 Azure DevOps 管道任务向 slack webhook 发送消息

> 原文：<https://dev.to/darraghor/how-to-send-a-slack-webhook-message-for-an-azure-devops-pipeline-task-247n>

当 Azure 上的部署完成时，我想发送一个 slack 消息。这很简单，但是我不得不从 slack、PowerShell 和 azure 的文档中收集一些东西。

这是我完成的剧本。

只需滚动到底部，向前跳过并查看整个脚本。

你必须在你的松散组织上创建一个 webhook url。我假设你已经做了那件事。

## Azure 中的 PowerShell 任务

需要在 Azure pipelines 中选择 PowerShell 函数任务，并将类型改为“inline”。你也可以把脚本和你的代码一起作为 ps1 文件，在这里引用它。这可能是一个更好的解决方案，一旦你进行了内联测试，这一切都为你工作。

## 脚本大纲

在脚本中，我创建了一个函数，然后立即调用它。该函数接受消息文本和 web 挂钩 url。

我向 slack 发送三个参数。

1.  你应该把`username`改成你喜欢的。“我的部署机器人”或类似的东西。
2.  文本稍后传入，将被替换。
3.  表情符号会出现在信息旁边。卷起的报纸很安全！:)

## 文本参数

要通过 webhook 引用 slack 中的用户，不要使用“@channel ”,必须使用“”

在这个例子中，我向构建添加了一个手动创建的链接。您必须将 url 更改为您自己的组织和项目，这样才能正常工作。

`<$($env:RELEASE_RELEASEWEBURL)|$($env:RELEASE_RELEASENAME)>`是发布在 slack 上的另一个有用的链接，将用户直接带到相关的发布。

在 PowerShell 中，您使用格式`$($env:BUILD_BUILDID)`来访问预定义的构建变量。注意，与 yaml 不同的是，我们通过 env 和“.”来获取它们对于 PowerShell，替换为“_”。

```
 function Send-SlackMessage {
    param (
        [Parameter(Mandatory=$true, Position=0)]$Text,
        [Parameter(Mandatory=$true, Position=1)]$Url
    )
    $body= @"
    {
        "username": "Loading Bay",
        "text": "MESSAGE_TEXT",
        "icon_emoji":":rolled_up_newspaper:"
    }
"@

    Invoke-WebRequest -Method Post -Uri $Url -Body $body.Replace("MESSAGE_TEXT","$Text") -ContentType 'application/json'
}
Write-Host "Sending slack message for BUILD: $($env:BUILD_BUILDID)"

Send-SlackMessage "<!channel> <https://##YOURVISUALSTUDIOORG##.visualstudio.com/##YOURPROJECTURL##/_build/results?buildId=$($env:BUILD_BUILDID)|Build: $($env:BUILD_BUILDID)> is now on STAGING" "https://hooks.slack.com/services/##YOUR/##WEBHOOK/##URL" 
```

Enter fullscreen mode Exit fullscreen mode