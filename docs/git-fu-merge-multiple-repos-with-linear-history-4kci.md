# Git-Fu:用线性历史合并多个回购

> 原文：<https://dev.to/detunized/git-fu-merge-multiple-repos-with-linear-history-4kci>

前几天，我给自己发明了一个新的难题:我想将我多年来构建的几个库合并到一个 repo 中，并一起重构它们。乍看起来很简单，将所有文件复制到子文件夹中，添加并提交:

```
$ git add .
$ git commit -m 'All together now!' 
```

搞定了。

不，不是真的。这将消除历史。我真的很想留着它。我经常回去查看对一个文件做了什么更改，我使用责备来查看某些代码被修改的时间和原因。我环顾四周，想知道如何快速完成这件事。我发现了一大堆描述类似项目的博客帖子。我还找到了代码片段、shell 和 Python 脚本，甚至 Java 程序来做这件事。

在尝试了其中的一些(不是 Java，不，谢谢)之后，我意识到它们并不完全符合我的要求。一些作者试图保留原始的提交散列。一些作者想要原始的文件路径。我希望能够从历史的起点追踪变化。

我发现的大多数方法都与我的目标不一致。通常人们试图将一个 repo 导入到一个分支中(通常通过从另一个 repo 添加一个`remote`)，在一次提交中将所有文件移动到一个子文件夹中，然后将该分支合并到`master`。这创建了一个大的提交，其中所有文件都被移动到一个子文件夹。然后是另一个巨大的合并提交，来自一个分支的所有更改被复制到另一个分支。当你在 GitHub 上查看这样的回购协议时，你会看到文件历史被破坏了(`blame`仍然有效)。

我还发现了一个内置命令`git subtree`,结果发现它和我之前尝试的所有第三方工具都有同样的问题。所以不去！需要在这里重新发明轮子，想出自己的解决方案。

因此，基本上，我需要找到一种方法来合并所有的回购，而不创建任何合并提交。我需要将原始文件移动到子文件夹中。我想到了两个 Git 工具:`cherry-pick`和`filter-branch`。

旁注。几年前我曾在工作中使用 Mercurial，效果非常好！Mercurial 上的用户体验令人惊叹。我有点希望它不要慢慢死去，让劣质产品接管开发场景。正如 Mercurial 直观且用户友好一样，Git 功能强大且用途广泛。Git 就像一套扭曲的乐高玩具:你可以用它来建造任何你想要的东西。

所以计划是这样的:

*   将每个回购放在自己的分支中
*   重写历史，将每次提交中的所有文件移动到一个子文件夹中
*   重写历史记录，将存储库名称添加到提交消息的前面
*   按照时间顺序从所有分支中挑选所有提交到`master`
*   删除分支
*   垃圾收集以收缩回购

很简单。今天感觉有点自虐，所以我们狂欢吧。

首先，像往常一样，当任何命令失败时，我们需要确保整个脚本失败。这通常会在出现问题时节省大量时间。通常都是这样。

```
#!/bin/bash
set -euo pipefail 
```

我想加入的回购列表:

```
repos="1password bitwarden dashlane lastpass opvault passwordbox roboform stickypassword truekey zoho-vault" 
```

确保我们从头开始:

```
rm -rf joined
git init joined
cd joined 
```

现在，这里有一个难题:

```
for repo in $repos; do git remote add $repo $REPO_DIR/repo
    git fetch $repo
    git checkout -b $repo $repo/master
    echo -n "$repo: " > prefix
    git filter-branch \
        -f \
        --tree-filter "mkdir -p .original/$repo && rsync -a --remove-source-files ./ .original/$repo/" \
        --msg-filter "cat $(pwd)/prefix -" \
        --env-filter 'GIT_COMMITTER_DATE="$GIT_AUTHOR_DATE"' \
        $repo
    rm prefix
done 
```

让我们一条一条地过一遍。首先，我将一个回购导入到它自己的分支中。名为`lastpass`的回购在名为`lastpass`的分支中结束。目前为止没什么困难。

```
git remote add $repo $REPO_DIR/repo
git fetch $repo
git checkout -b $repo $repo/master 
```

在下一步中，我重写了每个 repo 的历史记录，将文件移动到每次提交的子文件夹中。例如，来自 repo `lastpass`的所有文件都将在`.original/lastpass/`文件夹中结束。历史中的所有提交都会改变，就像所有的开发都是在这个文件夹中完成的，而不是在根目录下。

```
git filter-branch \
    -f \
    --tree-filter "mkdir -p .original/$repo && rsync -a --remove-source-files ./ .original/$repo/" \
    --msg-filter "cat $(pwd)/prefix -" \
    --env-filter 'GIT_COMMITTER_DATE="$GIT_AUTHOR_DATE"' \
    $repo 
```

`filter-branch`命令是一个多功能的野兽。有可能用它提供的所有可能的开关把回购变得面目全非。也有可能失败。其实超级简单。这就是 Git 在`refs/original/refs/heads`分支下创建备份的原因。为了强制覆盖已经存在的备份，我使用了`-f`开关。

当使用`--tree-filter`开关时，每次提交都被签出到一个临时目录，使用常规的文件操作，我可以重写提交。因此，对于每次提交，我都创建一个目录`.original/$repo`，并使用`rsync`将所有文件移入其中。

开关允许我重写提交消息。我想将回购名称添加到消息中，这样来自`lastpass`回购的所有提交看起来就像`lastpass: original commit message`。对于每次提交，脚本将在`stdin`接收提交消息，无论什么输出到`stdout`都将成为新的提交消息。在这种情况下，我使用`cat`来连接`prefix`和`stdin` ( `-`)。出于某种原因，我不明白为什么简单的`echo -n`不起作用，所以我必须将消息前缀保存到一个文件中。

最后一位`--env-filter`用于将提交日期重置为原始日期(Git 术语中的作者日期)。如果我没有这样做，Git 会将时间戳更改为当前时间。我不想那样。

下一步是将所有这些提交复制到`master`分支，以拉平历史。还没有`master`分支。我们做一个吧。出于某种原因，Git 创建了一个分支，将所有文件添加到索引中。用`git rm`杀死他们。

```
git checkout --orphan master
git rm -rf . 
```

要复制提交，我需要首先列出它们。这是通过`log`命令完成的:

```
git log --pretty='%H' --author-date-order --reverse $repos 
```

这个命令生成一个列表，列出了我之前创建的所有分支中从最旧到最新排序的所有提交散列。这一步的输出如下所示:

```
7d62b1272b4aa37f07eb91bbf46a33609d80155f
a8673683cb13a2040299dcb9c98a6f1fcb110dbd
f3876d3a4900e7f6012efeb0cc06db241b0540d6
7209ecf519475e59494504ca2a75e36ad9ea6ebe 
```

现在我有了列表，我迭代并把`cherry-pick`每个提交到`master`:

```
for i in $(git log --pretty='%H' --author-date-order --reverse $repos); do GIT_COMMITTER_DATE=$(git log -1 --pretty='%at' $i) \
        git cherry-pick $i
done 
```

环境变量`GIT_COMMITTER_DATE`再次用于将提交日期重置为最初的创建时间，这是我用`log`命令再次得到的，如下所示:

```
git log -1 --pretty='%at' <COMMIT-HASH> 
```

在这些步骤之后，我有了一个具有平面历史的回购，其中每个原始回购都位于自己的子目录中，位于`.original/`下。我可以使用 GitHub 文件历史和责备来查看原始文件自诞生以来发生的所有更改。由于 Git tracks 重命名，我可以将这些文件移动到它们在 megarepo 中的新位置，而我仍然可以获得历史记录和责备。

剩下唯一要做的事情就是清理回购，删除所有我不再需要的分支，运行垃圾收集器倒垃圾。

```
for repo in $repos; do git branch -D $repo
    git remote remove $repo
    git update-ref -d refs/original/refs/heads/$repo
done git gc --aggressive 
```

由此产生的回购生命[在这里](https://github.com/detunized/password-manager-access)。我觉得我投入的时间是值得的。这给了我一个机会来学习更多关于 Git 低级命令的知识。现在我有了一个 repo，我可以轻松地浏览它，而不需要每次想检查一些文件历史时都在分支之间跳转。

我使用的脚本可以在这里找到。