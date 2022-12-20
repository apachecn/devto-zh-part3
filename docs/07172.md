# 。gitignore 不起作用

> 原文：<https://dev.to/onurbilgin/gitignore-is-not-working-58c9>

即使到目前为止您还没有跟踪这些文件，git 似乎也能够“知道”它们，甚至在您将它们添加到。gitignore

注意:首先提交您当前的更改，否则您将会丢失它们。

然后从 git repo 的顶层文件夹运行以下命令:

```
git rm -r --cached .
git add .
git commit -m "fixed untracked files" 
```