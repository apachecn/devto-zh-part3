# 关于保存字符

> 原文：<https://dev.to/trezy/on-saving-characters-2240>

## 最近，我发现了一些令人兴奋的事情，我觉得我早就应该知道了。

**本文最初发表于 2013 年 5 月 6 日的[Trezy.com](https://trezy.com/blog/on-saving-characters)。**

当我在 CSS 文件中写我的无衬线字体系列堆栈时，我通常使用这个:

```
body {
 font-family: Ubuntu, Helvetica, Arial, sans-serif;
} 
```

Enter fullscreen mode Exit fullscreen mode

[Ubuntu](https://fonts.google.com/specimen/Ubuntu) 是我最喜欢的字体之一，我是从[谷歌字体](https://fonts.google.com)导入的，但是你可以用任何你想要的自定义字体替换它。因为比起 Arial，我更喜欢 Helvetica 字体，所以我希望他们已经预装了这种字体。如果不是，堆栈退回到 Arial，那么默认的`sans-serif`系统字体。

几年来，我一直用这种方式编写我的字体堆栈(没有自定义字体),完全忽略了一个可笑的明显事实，即堆栈的一半是不必要的。堆栈的`sans-serif`部分要求系统首选的`sans-serif`字体，默认情况下，在 Windows 上是 Arial，在 Mac 上是 Helvetica。如果我不担心用户改变了他们默认的`sans-serif`字体，我可以从我的字体堆栈中删除 16 个字符(不包括空格:

```
body {
 font-family: Ubuntu, sans-serif;
} 
```

Enter fullscreen mode Exit fullscreen mode

当我几天前意识到这一点时，我让自己盯着可怕的网站看了一个小时，作为对我无知的惩罚。从失败中恢复过来后，我决定仔细看看我的新字体堆栈。我唯一的问题是，我不相信我的 Windows 用户没有把他们的默认系统字体改成漫画字体或者 T2 纸莎草纸字体。幸运的是，如果你声明了 Helvetica 而它没有被安装，Windows 将回退到 Arial！当一切都结束时，我们的字体堆栈应该是这样的:

```
body {
 font-family: Ubuntu, Helvetica, sans-serif;
} 
```

Enter fullscreen mode Exit fullscreen mode

它不是一个性能保护程序，也不会给任何人留下深刻印象——事实上，如果你承认不知道这一点，你可能会看起来像个白痴——但在编写字体堆栈时，这是一条很好的信息。