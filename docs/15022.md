# 使用外壳别名更有效率

> 原文：<https://dev.to/easyaspython/be-more-productive-with-shell-aliases-2b16>

作为软件开发人员，最有价值的时间通常是花在合作和思考上的时间。为了获得尽可能多的时间，减少你用手指打字的时间！

## 去吧

Git 是一个无处不在的版本控制系统，随着许多开发人员开始在同一个代码库上合作，它变得更加重要。如果您的组织使用敏捷开发实践，您也将创建许多分支并大量更新`master`。

我使用 [`oh-my-zsh`](https://github.com/robbyrussell/oh-my-zsh) 的 [Git 插件](https://github.com/robbyrussell/oh-my-zsh/blob/master/plugins/git/git.plugin.zsh)，它为常见(和不太常见)的 Git 子命令提供了多种多样的别名。git 中的一个常见工作流是:

1.  查看`master`分支
2.  用来自远程的最新更改更新您的本地`master`分支
3.  为您的更改创建一个新分支
4.  使用[良好提交消息](https://chris.beams.io/posts/git-commit/)提交您的更改
5.  把你的树枝推到远处

没有任何别名，这个工作流在命令行上看起来是这样的:

```
$ git checkout master
$ git pull origin master
$ git checkout -b my-feature
# Make changes...
$ git add .
$ git commit -am 'Add my feature'
$ git push origin my-feature 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但是有时我在创建一个分支时会忘记那个讨厌的`-b`标志，而且我还会很恼火，因为我需要声明我正在推动哪个分支，而它几乎总是当前分支。

有了 Git 插件别名，这个工作流程需要做的工作就少多了:

```
$ gcm
$ gl origin master
$ gcb my-feature
# Make changes...
$ ga .
$ gcam 'Add my feature'
$ ggpush  # this takes advantage of oh-my-zsh's git_current_branch 
```

Enter fullscreen mode Exit fullscreen mode

有时你需要在查看另一个分支或在 Git 中做更复杂的事情时隐藏你的更改:

```
$ git stash
# Do some stuff and come back
$ git stash pop 
```

Enter fullscreen mode Exit fullscreen mode

但是这些别名派上了用场:

```
$ gsta
# Do some stuff and come back
$ gstp 
```

Enter fullscreen mode Exit fullscreen mode

有许多方便的别名，包括许多可以用于特定 Git 子命令的各种标志:

```
$ gd  # git diff
$ gdca  # git diff --cached
$ gds  # git diff --staged 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢使用它们，它们的简写通常比较直观，就像 Vim 一样。我添加了一些我自己常用的命令+标志组合，比如:

```
$ goop  # "git oops": git reset --soft HEAD~1
$ gun  # "git unstage": git reset HEAD -- 
```

Enter fullscreen mode Exit fullscreen mode

我还经常签出和更新 master，然后清理远程分支，以至于我只需要一个命令， [`upr`](https://github.com/daneah/dotfiles/blob/4b0b691013a8dcf5f3ef6e8f44157187ba6e14d2/source/functions.zsh#L10) (表示“更新回购”)，就可以完成所有这些工作。

## 巨蟒

我大部分时间都在用 Python 和 Django 工作。我在这个舞台上化名的轻松和长度反映了这一点；我运行命令越频繁，它的别名就越短:

```
$ python manage.py runserver  # Nope
$ ./manage.py runserver  # Nope
$ run  # There we go 
```

Enter fullscreen mode Exit fullscreen mode

```
$ pipenv shell  # Nope
$ psh  # Yep 
```

Enter fullscreen mode Exit fullscreen mode

```
$ pip install -r requirements.txt  # Hmmm
$ prq  # Indeed! 
```

Enter fullscreen mode Exit fullscreen mode

## 零零碎碎

有几个我经常使用的通用命令工具也足以让它们瘦身。我用的绝对最多的是`named`，扩展到`find . -name`。然后我就索要文件`named "*.py"`！我还通过给`grep -n --color=auto`起别名来默认`grep`使用颜色和行号。在极少数需要的情况下，我可以通过键入`\grep`来使用裸露的`grep`。

你可以在这里看到我所有的别名。

## 结论

所有这些的要点是，当使用命令行或编写代码时，你应该花时间*思考*你想做什么，尽可能少的时间*做*你想做的事情。我能够将节省下来的时间重新整合到更多的思考中——通常是关于我引入的所有 bug😉