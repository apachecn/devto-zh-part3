# Bash 函数& Git——如何清除对特定文件的更改

> 原文：<https://dev.to/stephencweiss/bash-functions-git-how-to-wipe-out-changes-made-to-a-specific-file-46b8>

我发现自己每天都要多次编写下面的命令。

```
git stash push pacakage-lock.json
git stash drop 0 
```

我读了一些关于 bash 函数的东西，想看看我是否能自动化这个过程。

由于我们的构建过程，我特别为两个文件运行了这些命令:`pacakge.json`和`package-lock.json`。

我的第一个解决方案是名为`cpkg`的硬编码版本。我将该功能保存在我的`~/.zshrc`中，以便于访问。

```
#cpkg = "clear changes to pacakge"
cpkg() {
  git stash push package-lock.json;
  git stash drop 0;
  git stash push package.json;
  git stash drop 0;
} 
```

这个功能实现了我的目标。它清除对`package.json`和`package-lock.json`的更改。

尽管如此，我还是想挑战自己，看看是否能增加一些活力。也许我不想清除我的包，而是从不同的文件中删除更改。

这需要一个接受参数的函数。

# 参数和 Bash 函数

当然，我首先看的地方是 Stackoverflow。这段对话很好地介绍了函数如何传递参数。

在我的例子中，我只需要处理一个参数——所以顺序并不重要，我可以使用`$1`而无需任何类型检查。

我想到的是:

```
#gsdf = "git stash drop file"
gsdf() {
  git stash push $1;
  git stash drop 0;
} 
```

很简单，但确实能达到预期效果。

如果我运行`gsdf package.json`,文件将被添加到存储中(如果有变化),然后被删除——让我平静地继续前进。

这是通过在执行期间将`package.json`赋给`$1`参数来实现的。

接下来的步骤是处理多个参数，这样我就可以一次传递多个文件。

# 告诫

这将*而不是*影响未跟踪或忽略的文件。它只会清除以前跟踪或提交的文件的更改。

原因是默认情况下`git stash`不会隐藏未跟踪或忽略的文件。

有关这方面的更多信息，请参见 [Stackoverflow](https://stackoverflow.com/questions/835501/how-do-you-stash-an-untracked-file) 上的不同对话。

# 最后的想法

这是实现我目标的最佳方式吗？可能不会，但它一直在为我工作，我理解它。如果你能给我指出一个更常见的 git 模式，我洗耳恭听。

如果有人知道一种处理未知数量的参数的好方法来创建一个循环，为每个传递给`gsdf`函数的文件重复这个过程，我也会很感兴趣。

# 资源

*   [高级 Bash 脚本-复杂函数](http://tldp.org/LDP/abs/html/complexfunct.html)