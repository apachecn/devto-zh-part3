# 早上例行公事-更新 git repos

> 原文：<https://dev.to/simonreynolds/morning-routine-updating-git-repos-2hhg>

*   开始工作
*   打卡上班
*   煮咖啡
*   从 git 获取最新的更改....![Morning routine - updating git repos](img/69b6a69fafc634d769b235c71b11f266.png)

浏览我经常与之交互的六个存储库涉及的打字量远远超过我愿意重复做的次数。

我想要的是遍历每个存储库并执行以下操作

*   `git fetch --prune`
*   如果我在默认分支(通常是主分支或开发分支)上，并且没有本地更改，那么就直接`git pull`

听起来非常适合自动化！PowerShell 拯救了我们，让我们有时间品尝咖啡