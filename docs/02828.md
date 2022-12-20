# Vim fzf -项目根

> 原文：<https://dev.to/serhatteker/vim-fzf-project-root-3jfi>

## fzf 是什么？

`fzf`是用`Go`编写的通用命令行模糊查找器。`fzf`是一个用于你的终端的模糊查找器，它是一个命令行应用程序，通过用户输入的查询从给定的输入中过滤每一行。当查询发生变化时，结果会实时更新。

## Vim

因为我花了很多时间在`Vim`中，试图通过名字或者某个文件中的代码来找到一个文件。简化这一过程非常重要。

你必须进行的每一次上下文切换都增加了开销，并且有可能失去对你要寻找的内容的关注。因此，它应该尽可能简单，例如:按一个键，输入查询，按回车键去匹配的文件。

在这里找到文件不是太大的问题。有一长串`Vim`插件提供使用模糊匹配或 MRU 算法的文件搜索。这方面的两个例子是`CtrlP`和`Command-T`。我曾经是`CtrlP`的粉丝，它总能成功完成任务。

## fzf.vim

`fzf`有一个小的内置`Vim`接口，已经可以工作了，但是它没有任何现有的功能。`fzf`的作者也写了这个`fzf.vim`插件。它是一个提供通用功能的小包装器。这包括列表文件，缓冲区，标签，git 日志和更多！

### 在目录中模糊搜索

从 CtrlP 来说，我需要的第一件事是替换模糊查找文件。解决方案是使用由`fzf.vim`提供的`:Files`命令。这列出了使用您的`$FZF_DEFAULT_COMMAND`环境变量的文件。在`enter/return`打开当前高亮显示的文件

问题是当你在当前的`buffer`中`return`一个新文件时，它改变了`:Files`目录。我不确定背景，所以可能是`:cd`或`:lcd`，但我不喜欢它。

我想使用一些“类似 IDE”的特性:它会找到项目根——如果有的话。git，。svn 等，如果当前目录不在某个项目下，还可以对其进行操作。

```
function! s:find_git_root()
  return system('git rev-parse --show-toplevel 2> /dev/null')[:-2]
endfunction

command! ProjectFiles execute 'Files' s:find_git_root() 
```

Enter fullscreen mode Exit fullscreen mode

所以这解决了我们的问题。因为我熟悉`<C-p>` keybing，所以我想使用这个绑定:

```
map <C-p> :ProjectFiles<CR> 
```

Enter fullscreen mode Exit fullscreen mode

Vim 再次成为开发人员工具箱中“最高效”的编辑器。

**链接:**

*   [fzf](https://github.com/junegunn/fzf)
*   [fzf.vim](https://github.com/junegunn/fzf.vim)