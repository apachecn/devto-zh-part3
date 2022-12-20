# 拉钻杆排放系统的外壳功能

> 原文：<https://dev.to/billah_tishad/shell-function-for-pulling-prs-156>

获取其他人打开的 PRs 来检查或测试新功能是一项常见的任务。一般来说，您应该这样做:

`git fetch upstream pull/<PR_NUMBER>/head:<BRANCH> && git checkout <BRANCH>`

每次都输入这个很快就会变得令人恼火，而且你不能给它起别名，因为 PR 号和分支机构名称会有所不同。

所以，shell 函数来帮忙了！函数可以带参数，所以在这种情况下它让我们的生活变得更容易。

因此，如果您想要上述命令的简写，请尝试以下方法:

*   将它放在 shell 配置文件中，如。巴沙尔或者。zshrc:

*   用`source ~/<Shell_Config_File_Name>`获取配置文件

*   使用`pr <PR_NUMBER> <USER:BRANCH>`获取和签出 PR 分支

shell 函数有点固执己见。我更喜欢按照作者在分支中最初的命名来命名分支。当你有很多分支时，这有助于保持跟踪。

GitHub 最近在分支名称旁边引入了一个复制按钮。

[![copy_button](img/2fb1be1efbec67e8881ba2a6b3b4c3b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lYsMGynx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://modasserbillah.ml/img/github_copy_button.png)

因此，只需点击那个按钮来复制分支机构名称就更容易了，但是它还附带了一个用户名。但是你不用担心，只要用按钮照原样复制它，并用它作为第二个变量来调用 shell 函数。该函数的第一行进行了拆分，只取分支名称作为新的分支名称。

黑客快乐！

*最初发布于:[http://modasserbillah . ml](http://modasserbillah.ml)T3】*