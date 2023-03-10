# 参考指南:创建 GitHub 存储库

> 原文：<https://dev.to/kymiddleton/reference-guide-create-a-github-repository-30f8>

欢迎阅读我的参考指南系列的第二部分！第一部分:参考指南:终端的常用命令。

GitHub 是一个基于网络的托管服务，使用 Git 进行版本控制。

**要创建存储库，请转到 GitHub:**

*   在网页右上角点击`+`符号，选择`New Repository`。
*   在提供的字段中输入存储库的名称。用破折号代替空格。
*   选择`Initialize This Repository with a README file`。
*   点击`Add .gitignore`上的箭头
    *   当文件被推送到资源库时，GitHub 需要忽略一些文件。例如:使用 Node.js 时，从列表中选择`Node`。

[![gitignore](img/532c1f38f4fb4a23032d41c30b270db1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zXS_kkzy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2t34d12rln5emyn0g7md.jpeg)

*   选择`Create repository`

**如何保护高手分支:**
来自 GitHub:

*   选择`Settings`选项卡

[![settings](img/113207151d2613e526951b3a7fa7d2e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tu99QTzO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1f5m4vw85fuvqdtsculn.jpeg)

*   从左侧的`Options`菜单中选择`Branches`
*   选择`Branch protection rules`右侧的`Add rule`框

[![branch protection](img/e011d5bf4c4f4fa76d2ce1d017f3a58c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P1pKlyEj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jsb8q623enodkg1gb87l.jpeg)

在屏幕的顶部会有一个输入分支机构名称的提示。

*   将规则应用于`Master`或分支名称
*   选择`Require pull request reviews before merging`
*   选择`Include administrators`
*   选择`Create`
*   选择`Save changes`

[![rule settings](img/ca2b8a204677e4ce7efec7e6a74b3d41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uBrGZ-7J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4efc7p04a61uv2mj6m7i.jpeg)

**克隆 GitHub 中的资源库:**
:

*   要克隆存储库，选择`clone`复制链接

在终端中:

*   进入`Terminal`并使用命令`cd`移动到存储和访问存储库的文件夹。
*   进入存储项目的文件或根目录后，输入以下命令。
*   `git clone <paste copied link from GitHub>`
    *   该步骤在本地文件和存储库文件之间创建了一个链接。

敬请关注[第三部分:](https://dev.to/kymiddleton/reference-guide-committing-changes-43el)提交变更。

对于完整的参考指南系列:

*   第一部分:参考指南:终端常用命令。
*   第二部分:创建一个 GitHub 存储库
*   第三部分:提交变更
*   [第四部分:](https://dev.to/kymiddleton/reference-guide-committing-changes-with-branches-2f8d)提交分支变更- [第五部分:](https://dev.to/kymiddleton/reference-guide-merge-conflicts-6op)合并冲突
*   第六部分:拉取请求
*   [第七部分:](https://dev.to/kymiddleton/reference-guide-conducting-a-code-review-k6m)进行代码评审- [第八部分:](https://dev.to/kymiddleton/reference-guide-complete-and-merge-a-pull-request--12n4)完成并合并一个拉请求