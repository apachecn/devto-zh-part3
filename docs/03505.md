# 如何在一行代码中从 git repo 中大量删除本地和远程标签

> 原文：<https://dev.to/nahuelhds/how-to-delete-local-and-remote-tags-massively-from-a-git-repo-in-just-one-line-k4g>

[![Resultado de imagen para git](img/cea0543873ab64e6789a63451c05d535.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gdlGw0WL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A4W4fdnO680ysRhFc9ppc8w.jpeg)

### TL；给我神奇的🧙‍♂️博士

> **警告！**这是一个破坏性的命令。使用风险自担

**用例:**您想要删除主版本 0 及其子版本。

首先，进行一次演习…

```
SEMVER=0 && git tag | awk "/^$SEMVER.\*/ { print \\$1 }" 
```

这样你就能看到你要删除的内容。

现在，如果你确定了，那就带来混乱……

```
SEMVER=0 && git tag | awk "/^$SEMVER.\*/ { print \\$1 }" | xargs -I % sh -c "git push origin :%; git tag -d %;" 
```

[![Thanos is so proud of you](img/b8c70d464a4f1cc5696d62c863ffd142.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pa1gwcms--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nahuelhds.dev/img/0__qTUgphZYwzkwxBYl.jpg) 灭霸为你感到骄傲

#### 备注

**SEMVER** 变量是在 **awk** 命令中使用的正则表达式。因此，如果您只想删除 0.1 版本及其补丁(0.1.1、0.1.2 等)。)，您可以在命令中使用`SEMVER=0.1`。

> **Windows 用户:** [你仍然可以使用 GitBash](https://www.atlassian.com/git/tutorials/git-bash) 。

### 我想要细节🤓

让我们再次查看删除主版本 0 及其子版本的完整命令。

```
SEMVER=0 && git tag | awk "/^$SEMVER.\*/ { print \\$1 }" | xargs -I % sh -c "git push origin :%; git tag -d %;" 
```

这个单行命令实际上做了以下事情:

1.  `SEMVER=0`声明 **SEMVER** 变量，供以后在 **awk** 命令中使用。
2.  显示本地 git 报告中可用的本地标签。
3.  `awk “/^$SEMVER.*/ { print \$1 }”`这里我们使用 **awk** 通过 **SEMVER** 变量中定义的正则表达式过滤所有匹配的版本。
4.  `xargs -I % sh -c "git push origin :%; git tag -d %;"`然后，我们使用 **xargs** 通过 percentage char (%)使用输入，并将其与 **sh** 命令结合。我们告诉 **sh** 通过`-c`参数从字符串中读取执行结果。最后，在字符串内部，我们执行 git 命令，首先远程删除标签，然后本地删除。注意，我们在这里通过 percentage char (%)使用了 **xargs** 输入。

希望有帮助！