# 计算 vim 中有多少段落

> 原文：<https://dev.to/voyeg3r/count-how-many-paragraphs-in-vim-jpe>

在我看来，计算段落数的最好方法是计算每段后面出现的所有空行。所以，让我们去掉连续的空行

```
:g/^\_$\n\_^$/d 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码找到一个空行开始，后面跟着另一个空行开始，并删除它。

如果最后一行不为空，让我们在最后一段后添加一个空行——(使用 put 命令，添加一个黑洞寄存器)

```
:$put_ 
```

Enter fullscreen mode Exit fullscreen mode

我们可以测试当前行是否为空

```
if getline('.') == ''
    echo "empty line"
endif 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们可以根据以下条件在文件的最后一行自动添加一个空行:

```
" if the last line '$' is not '!=' empty ''
if getline('$') != ''
    :$put _
endif 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们数一下有多少空行。

```
:%s,^$,,gn 
```

Enter fullscreen mode Exit fullscreen mode

上面命令的秘密是它不做任何事情，因为有标志“n ”,它只是显示如果没有使用标志“n ”,它会做多少次替换。

正则表达式...

```
^$ 
```

Enter fullscreen mode Exit fullscreen mode

...对应空行，因此我们实际上是在计算“每段之后”有多少空行。

当然，如果你愿意，你可以用所有这些
来定义一个函数

```
function! CountParagraphs()
    echom "Number of paragraphs: "
    silent! g/^\_$\n\_^$/d
    if getline('$') != ''
        $put _
    endif
    %s,^$,,gn
endfun 
```

Enter fullscreen mode Exit fullscreen mode

我们用了“沉默！”以便您可以只看到段落计数

为了在决定把它放在你的 vimrc 上之前测试这个函数，或者只是把代码保存在另一个地方，你可以运行:

```
:@+ 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以通过名字调用函数:

```
:call CountParagraphs() 
```

Enter fullscreen mode Exit fullscreen mode

当我写这篇文章时，我犯了一些错别字，我已经纠正了我看到的错误，但我的英语还远远不够完美，所以，请随意评论并建议在 vim 中更好的解决方案或使用其他工具的替代解决方案。