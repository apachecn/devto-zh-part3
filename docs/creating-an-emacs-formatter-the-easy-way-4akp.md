# 创建 emacs 格式化程序的简单方法

> 原文：<https://dev.to/erickgnavar/creating-an-emacs-formatter-the-easy-way-4akp>

几周前，我发现了一个非常好的 emacs 包，叫做[formatter . El](https://github.com/purcell/reformatter.el)。这个包允许以一种简单的方式定义重新格式化函数。

大多数语言都有重新格式化工具。仙丹有`mix format`，榆树有`elm format`，蟒蛇有`black`等等。这些格式化程序很方便，因为它们使代码一致，但这是另一篇文章的主题。

将这些格式化程序集成到我们最喜欢的编辑器中非常棒。这些是使用`reformatter.el`的足够理由(至少对我来说),所以让我们进入代码:

有一个简单的宏，允许我们用几行代码定义一个格式化程序。

对于这个例子，我们将使用`hindent` <sup id="fnref:hindent">[1](#fn:hindent)</sup> 为 haskell 创建一个格式化程序。

```
 (reformatter-define haskell-format
    :program "hindent") 
```

我们只需要定义将用于格式化代码的命令。在这种情况下`hindent`。这将创建一些有用的函数:

*   哈斯克尔格式
*   haskell 格式缓冲区
*   haskell 格式区域
*   haskell-保存时格式化模式

这些功能可以与键绑定一起使用:

```
 (define-key haskell-mode-map (kbd "C-c C-f") 'haskell-format-buffer) 
```

我们还可以设置 emacs 在文件保存时运行格式化程序，例如将这段代码放入您的`.dir-locals.el`中，它就会完成这项工作。

```
 (haskell-mode (mode . haskell-format-on-save)) 
```

用于格式化我们代码的程序需要能够从`stdin`读取并返回格式化的代码到`stdout`。在这种情况下`hindent`默认这样做。

在某些情况下，格式化程序默认不这样做。对于这些情况，我们可以使用格式化程序宏中的`:args`键向命令传递额外的参数。例如，`elixir format`默认接收一个文件或模式，但是我们可以使用`mix format -`改变它，现在它将从`stdin`中读取，所以我们需要将这些参数传递给我们的格式化程序。代码应该是:

```
 (reformatter-define elixir-format
    :program "mix"
    :args '("format" "-")) 
```

现在它将正常工作。

如果你不想仅仅为了格式化而安装一个外部包，这个包非常有用。我用这个包替换了`hindent-mode` (haskell formatter)和一个定制的 elixir formatter。该包也用于`elm-mode`包。

* * *

1.  [https://github.com/chrisdone/hindent](https://github.com/chrisdone/hindent)T2【归来】