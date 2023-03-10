# 参考指南:使用分支提交更改

> 原文：<https://dev.to/kymiddleton/reference-guide-committing-changes-with-branches-2f8d>

当我第一次开始使用分支的时候，我从来不记得在提交变更的时候我在哪个分支上。我的 go-to 命令很快变成了`git branch`。

如果您错过了该系列的部分内容:

*   第一部分:参考指南:终端常用命令。
*   第二部分:创建一个 GitHub 存储库
*   第三部分:提交变更

**用分支**提交变更
最好把`master`分支想象成生产。所以，最好永远不要推给总支。使用分支时，记住它们与主分支平行。一旦创建了一个新项目，就要养成在修改文件之前创建一个新分支的习惯。最好为每个设计特征创建一个新的分支。

**分支命令:**

*   `git branch`
    *   显示当前项目中的分支，包括主分支。

[![git branch](img/1ac3994cfd2b34ec31dc77ccd31e9c49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VLqDtRMK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u3cbvm06ppcg2axhemwb.jpeg)

*   `git checkout <branch name>`
    *   用于切换到不同的分支。

**新建一个分支:**

*   `git checkout –b <name of new branch>`
    *   `Checkout`类似于网站的主页，表示从主页或`master`分支切换到另一个页面视图或`branch`。
    *   启动要创建的新分支。

[![git checkout b](img/0e0452da3d4d0ddea5b4c5e2ced16af2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o_RvfD-K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ub81mpp6uya7i78y6qm.jpeg)

当代码准备好提交到分支时，使用以下命令:

*   `git status`*显示为红色的项目表示文件和后续更改未被跟踪。这些文件需要暂存并提交。
*   `git add –A`
    *   该命令启动被跟踪的变更，`A`表示`all`文件。这还会转换要登台的代码。
*   `git status`
    *   重复此命令将显示先前在`red`中突出显示的文件已切换到`green`，表明更改已被跟踪、保存并准备好推送到存储库。
*   `git commit –m “add comment description of changes made”`
    *   已暂存的程式码现在会储存在本机，并附上有关程式码的附注。使提交消息具有描述性和意义。
*   `git status`
    *   重复命令以验证所有更改都已暂存并准备好推送至存储库。
*   `git push origin <name of branch>`
    *   保存的更改被推送到指定的分支。

接下来:[处理合并冲突](https://dev.to/kymiddleton/reference-guide-merge-conflicts-6op)

对于完整的参考指南系列:

*   第一部分:参考指南:终端常用命令。
*   第二部分:创建一个 GitHub 存储库
*   第三部分:提交变更
*   [第四部分:](https://dev.to/kymiddleton/reference-guide-committing-changes-with-branches-2f8d)提交分支变更- [第五部分:](https://dev.to/kymiddleton/reference-guide-merge-conflicts-6op)合并冲突
*   第六部分:拉取请求
*   [第七部分:](https://dev.to/kymiddleton/reference-guide-conducting-a-code-review-k6m)进行代码评审- [第八部分:](https://dev.to/kymiddleton/reference-guide-complete-and-merge-a-pull-request--12n4)完成并合并一个拉请求