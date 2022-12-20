# 更改 Git 历史记录

> 原文：<https://dev.to/eesnaola/changing-the-git-history-3o3m>

在格式化我的工作电脑一个月后，我注意到我在 GIT 中配置的邮件是错误的。这导致我的提交不承认我是作者，也不会出现在我的帐户活动中。

如果在一次提交后检测到该错误，可以使用以下命令轻松解决:

```
git commit --amend --author="Correct Name <youremail@example.com>" 
```

Enter fullscreen mode Exit fullscreen mode

如果在几次提交后被检测到，就像我遇到的那样，最好的解决方案是这样的: