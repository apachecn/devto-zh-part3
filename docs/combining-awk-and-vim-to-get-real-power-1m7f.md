# 结合 awk 和 vim 获得真正的力量

> 原文：<https://dev.to/voyeg3r/combining-awk-and-vim-to-get-real-power-1m7f>

# 前言

在这个小“如何”的初始帖子之后， [@Sundeep](https://dev.to/learnbyexample) [评论](https://dev.to/learnbyexample/comment/9p6n):

> 为什么不使用 awk 本身来插入代码片段？因为您使用的是 gawk，所以如果您想进行现场编辑，也可以使用-i inplace
> 
> 我记不太清楚了，但我认为您可以使用静音来避免手动输入，请检查这些:...

我将解释这个场景，我的旧解决方案，然后是 gawk 解决方案

# 场景

我的个人 vimwiki 上有大约 500 个 markdown 文件，多年来我一直在做笔记，通过 Linux/vim 命令帮助自己和他人。最近我开始用 pandoc 把我的 markdown 文件转换成 html 和 LaTeX。

## 使用 vim + gawk

在我的`~/.config/init.vim`上有两个映射，可以很容易地将我当前的文档转换成 html 和 pdf。在转换的过程中，pandoc 警告说缺少*头衔*，这就把我带到了这里。

```
if !exists('*PandocMappings')
    function! PandocMappings()
        " NOTE: <F16> equals to Shift-F4
        noremap <F4> :! pandoc '%:p' -o /tmp/'%:p:t'.html --template=easy_template.html --toc && firefox /tmp/'%:p:t'.html &<CR><CR>
        noremap <F16> :! pandoc '%:p' -o /tmp/'%:p:t'.pdf --pdf-engine=pdflatex --toc && evince /tmp/'%:p:t'.pdf &<CR><CR>
    endfun
endif

augroup my_markdown
    autocmd!
    autocmd FileType markdown call PandocMappings()
augroup END 
```

Enter fullscreen mode Exit fullscreen mode

我的减价文件缺少元数据，大概是这样:

```
--------
file: the file name
lang: pt-BR
author: Sergio Araujo
date: abr 03, 2019
created: abr 03, 2019
abstrac: This aims...
-------- 
```

Enter fullscreen mode Exit fullscreen mode

手动添加了其中八个的元数据后，我意识到我有更好的工具和知识(包括网络搜索)来解决我的问题。元数据应该从第一行开始。

# 找出没有元数据的文件

使用 awk，更具体地说是 GNU awk“gwak ”,打印包含元数据的文件列表很简单

```
gawk '(FNR==1 && $1 ~ /^---/) {print FILENAME}' *.md

FNR==1  ...................... the first line equals to the first register
&& ........................... it allows us to test a second condition
~  ........................... match regular expression
/^---/ ....................... the regular expression itself 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令显示了我已经插入了元数据的文件，反转*正则表达式*非常简单，只需使用感叹号。因此，没有元数据的文件列表可以打印为:

```
gawk '(FNR==1 && $1 !~ /^---/) {print FILENAME}' *.md 
```

Enter fullscreen mode Exit fullscreen mode

# 将 awk 结果与 vim 结合

使用 shell *[命令替换](https://unix.stackexchange.com/questions/440088/what-is-command-substitution-in-a-shell)* 我们可以向 vim 提交我们想要的列表。

```
vim $(gawk '(FNR==1 && $1 !~ /^---/) {print FILENAME}' *.md) 
```

Enter fullscreen mode Exit fullscreen mode

# vim/neo vim argdo 命令

当您打开 vim 时，显示如下内容:

```
vim *.md 
```

Enter fullscreen mode Exit fullscreen mode

所有打开的文件都在 arglist 中，所以我们可以使用类似于:

```
:argdo command 
```

Enter fullscreen mode Exit fullscreen mode

# 插入头的 UltiSnippet 代码段

```
snippet headermd "markdown header" w
--------
file: `!p snip.rv = snip.fn`
lang: pt-BR
author: `!v g:snips_author`
date: `!v strftime("%b %d, %Y")`
created: `!v strftime("%b %d, %Y")`
--------
endsnippet 
```

Enter fullscreen mode Exit fullscreen mode

# vim 函数，可以被`argdo`调用来插入代码片段

```
fun! InsertHeaderMd()
    0pu_
    exe "normal! iheadermd\<C-r>=UltiSnips#ExpandSnippet()\<CR>"
endfun 
```

Enter fullscreen mode Exit fullscreen mode

```
0pu_  ............................ On the line zero insert black hole register (blank line)
exe normal! ...................... normal command
iheadermd ........................ enters insert mode and types headermd
\<C-r>= .......................... expression register
UltiSnips#ExpandSnippet()\<CR> ... the UltiSnippet trigger to call the snippet 
```

Enter fullscreen mode Exit fullscreen mode

# 整理好一切

我们甚至没有将函数(只是代码片段)放在配置文件中，您可以将函数复制到剪贴板，然后在 vim 上键入:

```
:@+ 
```

Enter fullscreen mode Exit fullscreen mode

现在，打开所有文件后，您可以:

```
:silent argdo call InsertHeaderMd() 
:silent argdo update 
```

Enter fullscreen mode Exit fullscreen mode

多亏了 [@Sundeep](https://dev.to/learnbyexample) 我添加了*静音*选项，这让我可以运行命令，而不需要敲很多次 Enter 键。他还建议用 awk 作为解决方案

# 使用带有 argdo 的 vim 宏

*打开没有元数据的文件*

```
vim $(gawk '(FNR==1 && $1 !~ /^---/) {print FILENAME}' *.md) 
```

Enter fullscreen mode Exit fullscreen mode

*创建 vim 宏*

```
:let @a=''
:let @a="ggO---\<Return>title: \<C-r>=expand('%:t')\<CR>\<Return>author: Sergio Araújo\<Return>abstract:\<Return>---\<Esc>" 
```

Enter fullscreen mode Exit fullscreen mode

*解释*

```
let @a='' ............................ delete any marcro 'a'
ggO .................................. got to the first line and open a line above
---\<Return> ......................... insert three dashes and a new line
\<C-r>=  ............................. starts a expression register
expand('%:t') ........................ expands the filename "t = tail" 
\<Esc> ............................... goes back to the normal mode 
```

Enter fullscreen mode Exit fullscreen mode

*运行 argdo 命令和一个宏*

```
:set hidden ............................ allows to go to the next file without saving

:silent argdo execute "normal! @a" | update 

:silent ................................. does not show any messages 
```

Enter fullscreen mode Exit fullscreen mode

# 呆呆的解着

这个解决方案来自一个@Sundeep [评论](https://dev.to/learnbyexample/comment/9p6n)

```
 var=$(date "+%b %m, %Y")
  awk -i inplace -v date="$var" 'BEGINFILE {print "--\nfile: "FILENAME"\ndate: "date"\nabstract: \n---"}; (FNR==1 && $1 !~ /^---/) {print}' *.md 
```

Enter fullscreen mode Exit fullscreen mode

任务完成。

## 解释 awk 长命令...

```
-i inplace ..................... makes possible edit files directly in awk
-v date="$var" ................. captures the content of var=$(date "+%b %m, %Y")
BEGINFILE ...................... insets some strings "between quotes"
FILENAME ....................... gawk variable that returns the file name
FNR==1  ........................ the first line equals to the first register
&& ............................. it allows us to test a second condition
~  ............................. match regular expression
/^---/ ......................... the regular expression itself 
```

Enter fullscreen mode Exit fullscreen mode

下一步将使用 shell *中的变量，这里是文档*。如果你们能帮我这个忙，我会非常感谢你们！

# 用 *sed* 可以吗？

我知道 sed 也可以在一堆文件中插入一个头，但是我应该编写一个 shell 脚本来获取每个文件名或者使用`-V` sed 选项。我想出的解决方案对我的问题很好，它也给了我足够的灵感来与你分享这个想法。请随意评论并提出替代方案或改进建议。(请纠正我的打字错误，我的母语不是英语)。

# 参考文献:

*   [https://stack overflow . com/questions/32481877/what-is-NR-fnr-in-awk](https://stackoverflow.com/questions/32481877/what-is-nr-fnr-in-awk)
*   [https://UNIX . stack exchange . com/questions/440088/what-is-command-substitution-in-a-a-shell](https://unix.stackexchange.com/questions/440088/what-is-command-substitution-in-a-shell)
*   [https://www . cyber Citi . biz/FAQ/how-to-print-filename-with-awk-on-Linux-UNIX/](https://www.cyberciti.biz/faq/how-to-print-filename-with-awk-on-linux-unix/)