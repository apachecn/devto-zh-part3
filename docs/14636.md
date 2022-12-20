# 刷新提交日期

> 原文：<https://dev.to/konstantin/refresh-commit-date-2cnh>

假设您进行了一些提交，并希望更新时间或日期。(这样你就可以只保留时间顺序了)

现在，如果您要在最近一次提交时这样做，您只需运行:

```
GIT_COMMITTER_DATE="$(date)" git commit --amend --no-edit --date "$(date)" 
```

Enter fullscreen mode Exit fullscreen mode

这将更新最后一次提交的日期，因此您可以`push`或`push force`它。

## 多次提交

要在批量模式下执行此操作，您只需运行:

```
git rebase HEAD~{number of commits} --ignore-date 
```

Enter fullscreen mode Exit fullscreen mode

你也可以在你的`~/.gitconfig`中创建一个快捷方式，比如:

```
[alias]
  refresh = "!f() { git rebase HEAD~$1 --ignore-date; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

其中参数是从`HEAD`开始更新的提交次数，即:

```
git refresh 6 
```

Enter fullscreen mode Exit fullscreen mode