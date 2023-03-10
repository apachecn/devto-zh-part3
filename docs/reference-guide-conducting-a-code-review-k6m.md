# 参考指南:进行代码评审

> 原文：<https://dev.to/kymiddleton/reference-guide-conducting-a-code-review-k6m>

代码评审是为协作者评审和批准代码变更的过程，协作者不是被推动的变更的作者。它基本上是由在受保护的主分支中工作于同一项目的同行进行的代码检查。关于代码审查过程和好处的更多细节，请点击这里查看我的帖子。

当收到审查变更的通知时，进入 GitHub 并访问项目存储库。

选择`Pull requests`选项卡。

*   `Pull requests`选项卡旁边的数字表示等待审查的请求数量。

[![Pull Request](img/ade0f5750c9d320f9adcc693a866242f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZBZgMwjy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xl26ylw2foiq6j1sixbg.jpeg)

从列出的项目中选择`Pull requests`。

[![Pull Request](img/b3bbcefbf08550bb01117a9c7d9f3212.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wSxgnayE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uvp32fzc0zfgjh2ag8qi.jpeg)

选择`Pull requests`后，新页面将显示几个细节:

*   要合并的提交数
*   更改的文件数量
*   贡献者分支机构名称

在 GitHub 中继续之前，记下贡献者`branch name`并返回到终端。进入项目根目录后:

*   `git fetch origin <branch name>`
*   `git checkout <branch name>`

从这里开始，运行应用程序来识别任何潜在的问题。

返回 GitHub，查看 pull 请求中的代码更改。

*   选择`Commits`标签或`Files changed`标签

[![PR Commits](img/f565815e09317e4f90f2bef1e86752f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S97e97AY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j56m5ft0epyy6mhjk760.jpeg)

**`Commits`标签页**

*   此选项卡显示为指定的拉请求提交的提交的历史记录
*   可以选择每个`commit`来查看所做更改的进度

**`Files changed`标签页**

此选项卡提供文件更改的并排比较。

*   `New`变化在右侧的`green`中突出显示。
*   在左侧，`old`变化在`red`中突出显示

流程的下一步是完成拉动式请求审查。该流程将提供机会在批准之前提出意见、批准已审核的变更或请求进行变更。因此，如果在签出应用程序并在本地运行时发现任何问题，请务必记下发现的具体问题。如果在`Files changed`选项卡中查看并排视图时有什么值得注意的地方，也一定要在流程的下一步提及细节[完成并合并拉动请求](https://dev.to/kymiddleton/reference-guide-complete-and-merge-a-pull-request--12n4)。

对于完整的参考指南系列:

*   第一部分:参考指南:终端常用命令。
*   第二部分:创建一个 GitHub 存储库
*   第三部分:提交变更
*   [第四部分:](https://dev.to/kymiddleton/reference-guide-committing-changes-with-branches-2f8d)提交分支变更- [第五部分:](https://dev.to/kymiddleton/reference-guide-merge-conflicts-6op)合并冲突
*   第六部分:拉取请求
*   [第七部分:](https://dev.to/kymiddleton/reference-guide-conducting-a-code-review-k6m)进行代码评审- [第八部分:](https://dev.to/kymiddleton/reference-guide-complete-and-merge-a-pull-request--12n4)完成并合并一个拉请求