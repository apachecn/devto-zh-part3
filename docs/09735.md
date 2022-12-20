# CSS 启动:计算纯 CSS 中的代码行

> 原文：<https://dev.to/gabrielecimato/css-power-up-count-code-lines-in-pure-css-67m>

我决定开始一个叫 **CSS 上电**的系列！我会收集一些关于简洁 CSS 特性的小帖子。有一件事让我印象深刻。不仅仅是因为它的直观性，还因为它广泛的浏览器支持。

厌倦了价格过高的笔记应用程序，或者无法提供我所需的免费应用程序，我决定创建自己的应用程序！这就是为什么我开始了一个兼职项目，重新发现了一个有趣的 CSS 属性。

这一切都是从`Slate.js`开始的，它提供了一个库来构建富文本编辑器。我在应用程序中寻找的一个功能是添加代码块的能力。我很快就找到了一个名为`slate-code`的插件来帮我做这件事。多方便啊！摆弄了一下之后，结果如下:

[![Code block](img/ee2c59f96cf224e8fc189790ccb29ed5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x55NNMnG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vmdlnv0gmerfm5aps01i.PNG)

现在这看起来很好，但是，我相信你已经注意到少了一些东西。**线数在哪里？有了耐心，我决定下兔子洞，确信这将是一次痛苦的旅程。起初，我考虑对每一行使用某种索引引用，并在旁边显示出来。但是处理行的删除和插入似乎过于复杂🤔。**

然后我想起你可以只用 **CSS 非常直观地做到这一点！**我激动不已😀！它的工作方式是创建一个命名的计数器，并指定哪个 CSS 类递增该计数器。除此之外，您还可以访问可以显示为`content`的计数器值。

让我们深入研究一下。首先让我们定义代码块的容器的类。

```
.code {
    background: #f7f9fa;
    border: 1px solid #e6e8eb;
    font-family: "Lucida Console",Monaco,monospace;
    overflow: auto;
    padding: 8px;
    white-space: pre;
    counter-reset: line;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`counter-reset: line`，我们定义并初始化一个名为`line`的计数器，其值为 0。

现在我们需要确定哪个类将负责递增计数器。我们为此使用了`counter-increment`属性，并给它我们想要增加的计数器名称的值，在我们的例子中是`line`。

最后，我们还想显示计数器值。将`counter(line)`与`content`属性结合使用，这正是我们得到的结果。作为一个额外的功能，你可以添加`-webkit-user-select: none`来防止用户选择计数器值。当您想要复制整个代码块时，这很有帮助。

```
.code-line::before {
    counter-increment: line;
    content: counter(line);
    -webkit-user-select: none;
    font-size: 10px;
    color: grey;
    margin-right: 10px;
    text-align: right;
    display: inline-block;
    width: 18px;
} 
```

Enter fullscreen mode Exit fullscreen mode

结果如下:

[![Code block](img/039e95eee4c1275f514a75efa3079275.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--61Wiydwr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rycv2ql79vrhui8twtzk.PNG)

其余的样式是相当随意的，由你决定。如您所见，添加像行数这样简单的东西非常容易。你可以在这里找到完整的参考文献[。起初，我对这种方法的简单性感到非常兴奋，然后我想起我必须检查浏览器支持。](https://thepracticaldev.s3.amazonaws.com/i/rycv2ql79vrhui8twtzk.PNG)

[![CSS Counters Support is over 95%](img/b877b7bfb07f3264040df67c6ed9030c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dFHH92GV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u57xnla3yeh9ccmyblwh.PNG)

🎉多么大的胜利！我们从对实现行计数器的方法感到恐慌，到拥有一个被广泛支持的简单 CSS 属性。我希望你和我一样对此感兴趣。有没有什么不太流行的 CSS 属性让你“啧啧称奇”？你以前用过行计数器吗？

如果你想看现场演示，可以在这里找到一个[。](https://codepen.io/Gabri3l/pen/PLdBxM?editors=1100)

👋你好，我是加布里！我热爱创新，在哈奇领导 R&D。我还喜欢 React、Javascript 和机器学习(以及其他许多东西)。你可以在 twitter [@gabrielecimato](https://twitter.com/gabrielecimato) 和 GitHub [@Gabri3l](https://github.com/Gabri3l) 上关注我。如果有任何问题，请在 Twitter 上发表评论或发送 DM！