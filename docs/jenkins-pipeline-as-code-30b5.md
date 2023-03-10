# 詹金斯管道代码

> 原文：<https://dev.to/harshadranganathan/jenkins-pipeline-as-code-30b5>

最初发表于[rharshad.com](https://rharshad.com/jenkins-pipeline-as-code/)

Jenkins Pipeline 是一套插件，支持将持续交付管道实施和集成到 Jenkins 中。

## 需要为詹金斯管道做代码

随着时间的推移，Jenkins 与大多数其他自托管 CI/CD 工具一样，带来了:

*   积累/创造大量就业机会的趋势
*   艰难且昂贵的维护
*   严重依赖用户界面
*   缺乏指定条件逻辑的强大方法

## 管道的特征为代码

*   管道被实现为代码，并且通常被检查到源代码控制中，作为真实的单一来源，使团队成员能够编辑、审查和迭代他们的管道。

*   在继续管道运行之前，管道可以选择停止并等待人工输入或批准。

*   管道支持复杂的现实需求

    *   有条件执行
    *   链接作业
    *   线性/复杂流动
*   Pipeline 插件支持对其 DSL 的自定义扩展，以及与其他插件集成的多种选项。

[https://imgur.com/WshehVd](https://imgur.com/WshehVd)

## IntelliJ 中的管道自动完成

要启用代码完成，请执行以下步骤:

1.  从您的 jenkins 下载管道 GDSL(http://(yourjenkinsurl)/job/(yourpipelinejob)/Pipeline-syntax/gdsl)。
2.  将 [Groovy SDK 支持](https://www.bonusbits.com/wiki/HowTo:Add_Groovy_SDK_to_IntelliJ_IDEA)添加到 Intellij。
3.  将 GDSL 文件放在项目的`src`目录下，并将`src`文件夹标记为`Sources Root`。
4.  打开 GDSL 的文件。现在，IntelliJ 将显示一条消息`DSL descriptor file has been changed and isn't currently executed.`。点击`Activate`。
5.  现在创建您的`.groovy` jenkins 文件并编写代码，自动完成将会工作。

## 代码片段生成器

您还可以使用 Jenkins 中可用的`Snippet Generator`特性(http://(yourjenkinsurl)/job/(yourpipelinejob)/pipeline-syntax/)为管道步骤生成代码片段，然后您可以在管道中使用这些代码片段。

[![](img/db426112543d3409fb29d5e66fbda9b1.png)](https://rharshad.com/assets/img/2018/05/jenkins-snippet-generator.png)

## 重播

Replay 特性允许您测试管道代码，而不必提交对 Repo 的更改。一旦你测试了代码，你就可以进行修改了。

[![](img/3af0ec4a3f5a4ba211c7efda03f0c670.png)](https://rharshad.com/assets/img/2018/05/jenkins-replay.png)

## 詹金斯声明式管道示例