# 变更日志:JSFiddle 液体标签现已上线

> 原文：<https://dev.to/link2twenty/changelog-jsfiddle-liquid-tags-now-live-1d1b>

我们有了另一个新标签，JSFiddle 标签！

JSFiddle 是另一个可以在线保存和分享代码的地方。这是一个制作快速演示的好地方，然后在你的博客文章中炫耀。

标准的样子是这样的

```
{% jsfiddle https://jsfiddle.net/link2twenty/7m6ej0m9/ %} 
```

Enter fullscreen mode Exit fullscreen mode

[https://jsfiddle.net/link2twenty/7m6ej0m9//embedded//dark](https://jsfiddle.net/link2twenty/7m6ej0m9//embedded//dark)

但是您也可以选择包括哪些选项卡以及它们应该以什么顺序出现。您可以拥有的选项卡有`js,html,css,result`。

```
{% jsfiddle https://jsfiddle.net/link2twenty/v2kx9jcd/ result,html,css %} 
```

Enter fullscreen mode Exit fullscreen mode

[https://jsfiddle.net/link2twenty/v2kx9jcd//embedded/result,html,css//dark](https://jsfiddle.net/link2twenty/v2kx9jcd//embedded/result,html,css//dark)

或者甚至只有结果面板

```
{% jsfiddle https://jsfiddle.net/link2twenty/xyycj5ta/ result %} 
```

Enter fullscreen mode Exit fullscreen mode

[https://jsfiddle.net/link2twenty/xyycj5ta//embedded/result//dark](https://jsfiddle.net/link2twenty/xyycj5ta//embedded/result//dark)