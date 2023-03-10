# 在 vim 中搜索 Ruby 方法声明

> 原文：<https://dev.to/pradyumna2905/search-ruby-method-declarations-in-vim-23fe>

虽然在 vim 中搜索方法声明可能很棘手，您可以使用`ctags`，但我发现使用`ag`或[silver searcher](https://github.com/ggreer/the_silver_searcher)会产生很好的结果。

我成为 vim 的活跃用户已经快 3 年了，主要开发 Rails 应用。我开始使用 vim 是因为我非常喜欢在一个屏幕上显示整个开发环境的想法。

运行您的服务器、测试、安装 gems、编写代码，无需离开或退出。

作为开发人员，我们偶尔会发现需要搜索方法声明。这里有几个`vimscript`函数，可以和`<Leader>`命令一起使用，有效地搜索方法声明，提高生产率。

```
function! SearchForDeclarationCursor()
  let searchTerm = expand("<cword>")
  call SearchForDeclaration(searchTerm)
endfunction 
```

这里我们只是展开并捕捉光标所在的单词。

```
function! SearchForDeclaration(term)
  let definition = 'def ' . a:term
  cexpr system('ag -w ' . shellescape(definition))
endfunction 
```

这个函数在单词`def`前面加上搜索词。这会给你一个类似于`def some_method`的字符串。`system`功能执行提供给它的任何`bash`命令。`-w`选项匹配精确的搜索词，从而给我们准确的结果。

最后，我们可以将它映射到一个`<Leader>`命令。

```
map <Leader>cd :call SearchForDeclarationCursor()<CR> 
```

希望这能帮助你！