# Git 的全局钩子

> 原文：<https://dev.to/eidsonator/global-hooks-with-git-2mmj>

最初发布在我的个人博客上。

# 用 Git 全局挂钩

## 简介

最近，我开始想知道是否有一种方法可以在我所有的 git 项目上运行一组 [git 挂钩](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks),以省去我在每个项目中创建/编辑挂钩的麻烦。是的，可以通过`git config --global init.templatedir`命令为新项目这样做，但是我有很多预先存在的 git 项目，我想给它们添加一个预推挂钩，并且我希望能够在影响我所有项目之后对这个挂钩进行修改。显然，我不是唯一需要这个特性的人，因为它是在 git 2.9 版中添加的。

## 详细信息

多亏了这个 [stackoverflow](https://stackoverflow.com/a/37293198) 答案，添加全局钩子非常容易，但是在设置了全局钩子路径之后，我确实遇到了一些问题。

### git 配置

要将路径添加到您的全局 git 配置，请运行以下命令:

```
git config --global core.hooksPath /path/to/my/centralized/hooks 
```

### 挂钩文件

下一步是添加钩子文件并使其可执行。

```
touch /path/to/my/centralized/hooks/pre-push
chmod a+x /path/to/my/centralized/hooks/pre-push 
```

然后添加你希望钩子执行的任何逻辑。我使用 [sonarqube](https://www.sonarqube.org/) 来为我的项目提供连续的检查，所以我希望`sonar-scanner`在每次推送时都运行。(我强烈推荐 [sonarqube](https://www.sonarqube.org/) 用于防止 bug 和代码气味，如果你不熟悉的话应该去看看！).我用的是[鱼壳](https://fishshell.com/)，所以你可能需要一个不同的 shebang。

```
# /path/to/my/centralized/hooks/pre-push
#! /usr/bin/fish

command sonar-scanner 
```

所以我创建了我的钩子，并做了一次测试，看看它是否能像预期的那样工作。然而，我只有一些不使用`sonar-scanner`的文档和小脚本项目。在推进这些项目时，我得到了一些难看的错误信息。这并没有导致推送失败，因为我没有检查`sonar-scanner`命令的返回代码，但是我仍然想隐藏这些消息，或者根本不在不需要的项目上运行`sonar-scanner`。这很容易做到，在运行扫描仪之前检查是否存在`sonar-project.properties`文件。

```
# /path/to/my/centralized/hooks/pre-push
#! /usr/bin/fish
if test -e ./sonar-project.properties
    command sonar-scanner
end 
```

好了，现在它只在应该运行的时候运行，但是我碰到了另一个问题。我有一些项目有特定于项目的预推送挂钩，我希望它们在推送执行之前仍能运行。我假设添加这些全局钩子是对项目钩子的补充，而不是覆盖它们。然而，这不是我想要的行为；我仍然希望执行任何特定于项目的挂钩。修复方法是检查项目级 git 挂钩是否存在，如果存在就触发它。我认为在执行全局钩子逻辑之前，让我的用例触发它更有意义。

```
# /path/to/my/centralized/hooks/pre-push
#! /usr/bin/fish

if test -e ./.git/hooks/pre-push
    command sh ./.git/hooks/pre-push
end

if test -e ./sonar-project.properties
    command sonar-scanner
end 
```

## 结论

所以我有我的全局钩子，它只在必要的时候运行`sonar-scanner`,并确保也执行任何局部钩子。我确信我只是触及了它的皮毛，我期待着在不久的将来添加更多的功能。接下来，如果[索纳库](https://www.sonarqube.org/)质量门失败，我将调查推动失败的原因。改善我的 CI/CD 工作流程。对于更好的挂钩有什么建议或者想法吗？请在下面的评论中留下它们。