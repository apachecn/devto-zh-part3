# 生成传统变更日志

> 原文：<https://dev.to/nikoheikkila/generating-conventional-changelogs-4n39>

作为一名开源维护者，我非常痴迷于信息丰富的发布说明，每次看到发布说明中可怕的*“错误修复和微小改进”*行，我都会退缩。我喜欢尽可能准确地写我的发行说明，这是繁琐的手工操作。我决定使用脚本功能来缓解它。

我制作的脚本使用了友好的交互式 Shell(您也应该使用),但是在 Bash、ZSH 中复制相同的功能，甚至将它作为 Git 别名包含进来，都是微不足道的。

## 安装和使用

你可以找到这个脚本作为[要点](https://gist.github.com/nikoheikkila/96f734a5dffa7a9e6e32c33e8b2c7ddc)，并把它放在你的 Fish 函数中。这里我使用我的 Python 库`pwnedapi`作为例子。

```
curl -sSL https://gist.githubusercontent.com/nikoheikkila/96f734a5dffa7a9e6e32c33e8b2c7ddc/raw/3b98f241e203631915729d165ecf6e767bbff7ca/changes.fish -o ~/.config/fish/functions/changes.fish

changes v1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

```
## `v1.0.0`

Write your release notes on this line.
 - docs: add a security policy (_Niko Heikkilä_) (d02e70a)
- fix(security): upgrade Python packages (_Niko Heikkilä_) (bbb0861)
- fix(tests): replace parameter message with matches in pytest.raises (_Niko Heikkilä_) (9161cf9)
- release: 1.0.1 (_Niko Heikkilä_) (f9cdd53) 
```

Enter fullscreen mode Exit fullscreen mode

该脚本只接受一个参数，即我们用来比较状态的提交。它很快，包括所有必要的信息，并完全支持 Markdown。我还能要求什么？

## 菜谱

当然，你不应该盲目相信从互联网上下载的脚本，所以让我们来看看脚本是做什么的。完整的脚本可以在下面看到。

```
function changes -d "Generate a Markdown changelog from conventional commits" -a target

    # Use fallback variables if no arguments were given.
    if test (count $argv) -eq 0
        set target master
    end

    # Include commit message, author name, and the short hash in parentheses.
    set -l log_format "%s (_%aN_) (%h)"

    # Compare against HEAD (the latest commit).
    set -l source "HEAD"

    # Filter to commits that pass the conventional commit format.
    # See: https://www.conventionalcommits.org/
    set -l commit_filter "^[a-z]+(\([a-z]+\))?:\s.+"

    # Prefix each line with '- ' to render a Markdown list.
    set -l prefix '{print "- " $0}'

    # Write changelog header
    echo -e "## `$target`\n\n"
    echo -e "Write your release notes on this line.\n\n"

    # Fill and sort the actual changelog
    git log --oneline --pretty=format:$log_format $source...$target \
    | grep -E "$commit_filter" \
    | sort -k1 \
    | awk "$prefix"

end 
```

Enter fullscreen mode Exit fullscreen mode

这个工具的核心自然是,`git-log`,它是一个强大的工具，可以发现你的项目中正在发生什么。这里我们用它来打印一个紧凑的单行日志，用自定义模式格式化每条消息，其中:

*   `%s`是提交标题
*   `%aN`提交作者的名字是否与`.mailmap`文件相关(如果有的话)
*   `%h`是短格式的提交散列

我们在 changelog 中包含提交哈希的原因是 GitHub 和 Gitlab 会自动将这些哈希链接到它们各自的提交页面，以便查看整个差异。这是检验提交是否实现了它所声明的内容的一种非常方便的方法。

Git 可以从一系列提交中生成一个日志。因为分支和标记只是指向一个提交的指针，所以我们可以在这里指定它们。我们将从`HEAD`或克隆的当前状态与给定的指针进行比较，以产生最近更改的列表。

接下来，我们通过一个正则表达式过滤器将日志输出传送到`grep`工具，该过滤器要求提交标题通过 [*常规提交*](https://www.conventionalcommits.org/) 符号。如果你愿意，你可以修改这个过滤器，但是我想让它保持原样，以强制我的团队使用共享提交模板。如果您不熟悉常规提交，我强烈建议您阅读前面提到的链接，并将其应用到您的工作流程中。

接下来，我们希望对这些提交进行分组，这样每个提交标题前缀无论是`feat`、`fix`、`docs`还是其他什么都会粘在一起，而`sort`就是解决方案。最后，为了充分利用 Markdown 语法，我们使用`awk`在每个提交行前面加上一个破折号。

## 奖金:以任意格式导出

现在我们有了 Markdown 中的 changelog，多亏了 [Pandoc](https://pandoc.org/) ，我们可以把它放在任何地方，以任何我们想要的格式。要将 changelog 转换成 HTML 格式，就像这样(替换`html`以尝试其他格式):

```
changes v1.0.0 | pandoc -f markdown -t html 
```

Enter fullscreen mode Exit fullscreen mode

```
<h2 id="v1.0.0"><code>v1.0.0</code></h2>

<p>Write your release notes on this line.</p>

<ul>
  <li>docs: add a security policy (<em>Niko Heikkilä</em>) (d02e70a)</li>
  <li>fix(security): upgrade Python packages (<em>Niko Heikkilä</em>) (bbb0861)</li>
  <li>fix(tests): replace parameter message with matches in pytest.raises (<em>Niko Heikkilä</em>) (9161cf9)</li>
  <li>release: 1.0.1 (<em>Niko Heikkilä</em>) (f9cdd53)</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

下次你需要为你的项目设计一个新的版本时，试着用一个伟大的变更日志来增加它的味道。✨🍰