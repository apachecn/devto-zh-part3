# 不要提交那个文件！

> 原文：<https://dev.to/bronzehedwick/dont-commit-that-file-n80>

我写了一个小的 git `pre-commit`钩子来防止提交某些文件。还有更多的话要说，但是如果你不耐烦，你可以[直接跳到商品](#the-code)。

在工作中，我们在 git 中跟踪了一些配置文件，我们在本地修改这些文件以启用调试选项。我们不想忽略这些文件，不得不在 git 之外的不同系统中管理它们，但是我们也不想签入调试选项。

因此，我们在 git 中跟踪这些文件，并在本地系统上修改它们，并且尽量记住不要签入那些调试选项。

在调试更改以我的一个拉请求结束后，我有了一个想法:既然我是一个计算机程序员，如果我能使用我的计算机来拯救我自己呢？这是足够疯狂的工作。

我真正想要的是 git 阻止我提交对这些文件的更改，如果有必要的话，可以是物理的。答案: [git 钩子](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)。

Git 挂钩是定制的脚本，当执行提交、合并等操作时，它会在本地存储库中运行。它们非常强大，因为它们可以是在 shell 中运行的任何脚本，但是像计算机科学中的大多数东西一样，它们仍然不能出拳。这意味着我的脚本需要抛出一个错误来阻止我提交那些调试更改。

几分钟后，我拼凑了一个 git `pre-commit` hook 脚本，防止任何不需要的文件被更改。顾名思义，预提交钩子在提交发生之前运行*，所以如果一个禁忌文件在变更集中，当我运行`git commit`时，我会得到一个很大的错误。*

这是我想到的:

```
#!/bin/sh
#
# This script prevents specific file modifications from taking place.
# We want certain config files checked into git so that builds work on a clone,
# *and* we need to modify these files locally to enable debug options.
# This leads to a scenario where we can accidentally check in the config files
# with our local debug options checked in. This script prevents that.

# Get current revision to check against.
if git rev-parse --verify HEAD >/dev/null 2>&1
then against=HEAD
else
  # Initial commit: diff against an empty tree object
  against="$(git hash-object -t tree /dev/null)"
fi

# Redirect output to stderr.
exec 1>&2

# Test staged files against the files we don't want to check in,
# and abort if found.
git diff --cached --name-only "$against" | while read -r file;
do
  if test "$file" == "path/to/my/unchanagble/file.yml";
  then echo "Don't check in file.yml. Aborting!"
    exit 1
  fi

  if test "$file" == "some/other/file.php";
  then echo "Don't check in file.php. Aborting!"
    exit 1
  fi

  # Repeat pattern as necessary.
done 
```

Enter fullscreen mode Exit fullscreen mode

魔酱接近尾声；我循环查看`git diff --cached --name-only`的输出，它显示了每个暂存文件的名称，并检查文件名是否与我不想提交的文件之一相匹配。如果文件匹配非零状态的`exit`，git 会很高兴地阻止我提交。万岁！