# Git 文件重命名问题(删除/添加而不是重命名)

> 原文：<https://dev.to/nickytonline/renaming-a-file-via-git-after-it-has-been-deleted-in-a-pr-41dn>

嘿，伙计们，我需要一点帮助，因为我已经达到了我的极限。

我正在[处理一个 PR](https://github.com/sindresorhus/refined-github/pull/1750#issuecomment-460886960) 并且我当时没有意识到，但是当我重命名一些文件时，一些文件被提交为一个删除的文件(原始文件)和一个新文件(旧文件的重命名)。我被要求确保它在 Git 中被重命名，以便保存历史。很公平。

因此，如果你看一下 PR 中的评论(我在那里链接到第一个关于重命名的评论)，你会看到我在几个不同的 was 中做了以下事情，但这里是它的要点:

*   恢复了删除的文件，`source/libs/icons.js`
*   删除了新文件，`source/libs/icons.tsx`
*   提交这些更改
*   运行一个`git mv source/libs/icons.js source/libs/icons.tsx`
*   按下遥控器，PR 显示为文件重命名，没有文件更改。
*   下一次提交是放回我在`source/libs/icons.tsx`中的更改。
*   推至遥控器，PR 显示`source/libs/icons.js`已删除`source/libs/icons.tsx`已添加。

我甚至有一个回购维护者试图帮我，但他最终陷入了和我一样的困境。

有什么建议吗？

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/search/photos/source-control?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片