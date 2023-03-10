# XDG 数据之家到底是什么？

> 原文：<https://dev.to/captainsafia/what-the-heck-is-xdgdatahome-1il3>

在过去的一天里，我一直致力于在 nteract monorepo 中创建一个新的 [`@nteract/fs-kernels`包](https://github.com/nteract/nteract/pull/3870)。除了具体的实现细节之外，这个新的包将多个包的功能合并为一个，并在它们之上添加了一个简洁的 API 层。

为了让它工作，我必须从 [jupyter-paths](https://github.com/nteract/jupyter-paths) 中引入源代码。

```
// TODO: respect XDG_DATA_HOME
return home(".local/share/jupyter"); 
```

嗯。`XDG_DATA_HOME`到底是什么，为什么我必须尊重它？在这种情况下，我做了每个开发人员可能会做的事情:我做了一个快速的谷歌搜索。在浏览了第一页上的几个结果后，我得出了这个待办事项评论的结论。

`XDG_DATA_HOME`是一个目录，用于在某些 Unix 操作系统上存储特定于用户的文件。

在深入研究之前，我应该向您展示上面调用的`home`函数的实现。

```
function home(subDir?: string) {
  const baseDir = homedir();
  return subDir ? path.join(baseDir, subDir) : baseDir;
} 
```

该函数实际上将一个目录路径连接到用户的主目录。上面的函数中调用的`homedir`函数是在 Node 的`os`模块中定义的`homedir`函数。

所以事实证明，`XDG_DATA_HOME`默认为`$HOME/.local/share`，上面的代码片段返回的就是这个值。所以对上面待办事项的修复非常简单，我像这样更新了代码。

```
return process.env.XDG_DATA_HOME || home(".local/share/jupyter"); 
```

呜！是时候准备和提交了。

无论如何，我认为写一篇博文展示我在编码时遇到的一些日常事情会很有趣。可以说，我今天做了更有趣的事情，但是当我有这个想法的时候，我已经在做更有趣的事情了。

下一篇博文再见！