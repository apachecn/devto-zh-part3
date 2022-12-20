# 很酷的东西:弹出回调

> 原文：<https://dev.to/michaelsolati/something-cool-popup-callbacks-2jfo>

# 问题

假设您需要将一个用户指引到一个不同的网页/域，并且想知道他们什么时候完成了您需要他们做的任何事情。

# 背景

我目前正在开发一个需要 OAuth 的 Wordpress 插件，这样我就可以允许他们做一些事情了！(我想我现在不能透露...)虽然我可以解释一两个重定向 URIs，但我不可能处理我的 WORDPRESS 插件运行的每个域！！！！

# 解

我在插件中选择做的是当用户点击 OAuth 按钮时打开我托管的页面。

```
const newWindow = window.open('https://somesite.com/oauth', 'oauth', 'height=720,width=480'); 
```

Enter fullscreen mode Exit fullscreen mode

一旦创建了新窗口，我就可以通过添加函数
来扩展当前的`window`对象

```
window.popUpCallback = (code) => {
    newWindow.close();
    console.log(code);
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，在我的弹出窗口中，将处理授权，在它获得授权代码后，它可以调用我添加到窗口中的函数:

```
window.opener.popUpCallback(value); 
```

Enter fullscreen mode Exit fullscreen mode

这里发生的事情是，我的弹出窗口将调用我添加到打开当前(新)`window`的`window`中的`popUpCallback`函数。

回头看看我的`popUpCallback`函数，你会看到我关闭了`newWindow`(弹出窗口)，然后关闭了`console.log`我得到的代码。但是你现在可以做任何你想做的事情。你所做的是在窗口之间传递一个值！

对我来说，这是完美的，因为现在我只需要有一个重定向 URI，无论这个插件在哪里运行，但也许这将是有益的，因为一些其他原因。

尽情享受吧！

* * *

要了解我正在做的一切，请在 [Twitter](https://twitter.com/MichaelSolati) 上关注我。如果你在想，*“给我看看代码！”你可以在 [GitHub](https://github.com/MichaelSolati) 上找到我。*