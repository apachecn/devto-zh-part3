# 从远程历史记录中删除意外提交的文件

> 原文：<https://dev.to/jenc/removing-accidentally-committed-files-from-remote-history-3acj>

看起来像是*偶然*将一些 api 密钥或`node_modules`或敏感数据推入公共回购的一种仪式。然后在慌乱中我`git rm -r`了这个文件并提交了下一个(...但这只是打破了它)和在一个恐怖的飞行，武力推动编辑和 rebased 历史。但是浏览一下我的 github 提交历史会显示在`diff`中，并且那些有罪的文件仍然牢牢地植入我的远程回购中。

我对自己极度活跃的 git 历史也变得有些偏执。推动一个或三个提交来解决每一个小问题现在看起来很糟糕，因为我不能声称自己刚刚从训练营出来:/

来回复位还原半小时后我学到的东西:

*   如果旧的提交已经被推送到远程存储库，那么在 rebase 期间选择先前提交列表中的`drop`(即`git rebase -i HEAD~n`)不会删除旧的提交！
*   用`git rm -r`递归删除一个文件并不会把它从我的本地文件系统中删除。它从工作目录历史中删除文件。
*   `git rm --cached <filename>`不从工作树中删除文件。它只是对文件进行拆分，随后的提交确保它不会被 git 跟踪。它删除从文件到索引的路径。相关:[停止追踪，开始忽略](https://dev.to/svijaykoushik/stop-tracking-and-start-ignoring-ook)作者:斯维亚克。
*   取消文件的存放。
*   `git revert HEAD <filename>`对文件级别没有影响。
*   丢弃指定提交散列之后的本地提交，但前提是它尚未被推送。
*   如果已经提交了，`git revert HEAD~n`或`git revert`将创建一个新的提交，撤销现有的提交，并在新的提交中比较和应用对项目的更改。-完成上述所有操作后，在以前提交的列表中选择`squash`并不会删除包含旧文件/文件夹的提交。

啊啊啊！

此时，如果它还是相当新的，我删除远程回购，在 github 上创建一个新的，然后用我“重写”的 git 历史`git remote add origin <new github ssh>`。(操作方法如下)

[![pruning a tree](img/ce4cacb16aa225e95e31daf9f2d4e942.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q2DInHrB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://lifescapecolorado.com/wp-content/uploads/2016/03/landscape-1435351385-pruning-guide-index.jpg)

> "像讲故事一样对待每一次承诺."-前任经理。

哦，前任经理高级开发，我想念你。我们进行了许多很棒的配对活动。你最酷的一点是，你会向我展示你是如何做的，而不会给出免责声明，这些免责声明可能会阻塞我的思维，并在我知道我哪里搞砸了时支持我。顺便说一句，我不是在讽刺你。

在逛了 StackOverflow 之后，我在[上看到了这个用户使用 git filter-branch](https://stackoverflow.com/a/2158271) 的回答。这是他们惊人命令的浓缩解释:

1.  `git filter-branch --index-filter "git rm --cached -f -r --ignore-unmatch filenameOrFolderName" --tag-name-filter cat -- --all`

`--index-filter`在历史的每一步都与索引相对应。当文件存在时，命令`git rm -r --cached -f --ignore-unmatch <filename>`递归地删除该文件。

1.  `git update-ref -d refs/original/refs/heads/master`
    一些谷歌搜索告诉我，这个命令删除了原始主分支中的引用头，并将引用更新为 top reflog 条目的值。

2.  `git reflog expire --expire=now --all`
    “参考日志”或 reflogs，记录您的本地 repo 中何时更新了分支和其他参考的 tip。与`expire`和`--all`一起，reflog 中所有旧的和当前的条目都将被删除。

3.  `git gc --prune=now`
    显然 gc 就像是海量压缩和垃圾拣选。这使得整个 git 历史的规模变小了。

4.  `git push`
    将此推回到远程存储库！

如果我没有正确理解这些命令，请随时纠正我。我通常尝试任何事情，直到它的工作，这一个似乎对我最有效。

我也很想听听你是如何删除以前在本地或远程提交的不想要的信息的！

< 3 git <3

来源:
[从存储库的历史中删除(未推送的)文件| Github](https://help.github.com/articles/removing-files-from-a-repository-s-history/)
[从存储库中删除敏感数据| Github](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)
[从 Git 中的分支删除提交](https://stackoverflow.com/questions/1338728/delete-commits-from-a-branch-in-git)
[重置，签出&恢复| Atlassian bit bucket](https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting)
[Git | Git-GC](https://git-scm.com/docs/git-gc)