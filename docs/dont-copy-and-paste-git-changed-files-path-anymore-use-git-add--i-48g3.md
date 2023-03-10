# 不要再复制和粘贴 git 更改的文件路径，使用' git add -i '

> 原文：<https://dev.to/luciunknown/dont-copy-and-paste-git-changed-files-path-anymore-use-git-add--i-48g3>

当新手开发人员在 git 库上进行一些更改时，经常会看到他们复制和粘贴路径。避免这种情况的一个好方法是使用“git add -i”并选择您想要添加的文件。

**对-i 参数的简要说明**

"*将工作树中修改的内容交互添加到索引中。可以提供可选的路径参数，以将操作限制到工作树的子集。详见*“互动模式”(【https://git-scm.com/docs/git-add】T2)

`1 - Inside a git repository type git add -i`

`2 - Type 2 to update`

`3 - Type the file numbers to add, you can specify comma separated values (1,2,3) or an interval (1-7) to add at once.`

`4 - Hit Enter`

`5 - Type 7 to quit interactive mode`

`tip: To remove some file you can type -fileNumber. Ex: -5,-1`

[![git add -i example](img/d5a0cc1ec334479f610aed38e0c04394.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a1NvD3yG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/szp78xrlds1kys38pnrc.png)

:) :) :)