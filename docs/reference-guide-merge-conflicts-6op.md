# 参考指南:合并冲突

> 原文：<https://dev.to/kymiddleton/reference-guide-merge-conflicts-6op>

欢迎回到参考指南系列。

*合并*是将来自另一个分支的代码变更整合到一个工作分支中的行为。使用“Git ”,如果没有干预，有时无法解决竞争的变更。当某人编辑了一个文件，而另一个人删除了同一文件，或者对同一文件的同一行进行了更改时，可能会发生冲突。

合并冲突一开始可能有点吓人，但是放心，不会太久的。稍加练习，你很快就会成为职业选手。

**解决合并冲突**

一旦对分支名称进行了修改，在 GitHub 中创建`pull request`之前，最好再遵循一些步骤来解决潜在的冲突。

*   `git pull origin master`
    *   该命令更新本地分支以匹配主分支。

[![git pull](img/b2d65efd944855f28ab68fcb2bbbf498.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xfFqSFlY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d7qyo4f8zm732cvzkz1i.jpeg)

打开选择的代码编辑器来查看更改或冲突。一般来说，`current`变更会以一种颜色突出显示，而`incoming`变更会以不同的颜色突出显示。接受`current`或`incoming`变化。完成后，运行以下命令:

*   `git status`
*   `git add –A`
*   `git status`
*   `git commit –m “add comment description of changes made”`
*   `git status`
*   `git push origin <name of branch>`

接下来:[拉请求](https://dev.to/kymiddleton/reference-guide-pull-requests-5an9)

对于完整的参考指南系列:

*   第一部分:参考指南:终端常用命令。
*   第二部分:创建一个 GitHub 存储库
*   第三部分:提交变更
*   [第四部分:](https://dev.to/kymiddleton/reference-guide-committing-changes-with-branches-2f8d)提交分支变更- [第五部分:](https://dev.to/kymiddleton/reference-guide-merge-conflicts-6op)合并冲突
*   第六部分:拉取请求
*   [第七部分:](https://dev.to/kymiddleton/reference-guide-conducting-a-code-review-k6m)进行代码评审- [第八部分:](https://dev.to/kymiddleton/reference-guide-complete-and-merge-a-pull-request--12n4)完成并合并一个拉请求