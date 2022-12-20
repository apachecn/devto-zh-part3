# 停止使用内联 CSS(赞实用类！)

> 原文：<https://dev.to/jacobmparis/stop-using-inline-css-praise-utility-classes-3lkb>

你以前已经听过这些了——代码风格的福音传道者从天堂宣讲`Separation of Concerns`和`Clean Code`。通常很难和他们争论。HTML 用于布局，CSS 用于样式。不管怎样，大部分是。因此，作为一个物种，我们集体决定停止编写这样的代码:

```
<p style="font-weight: bold; text-align: right;" /> 
```

Enter fullscreen mode Exit fullscreen mode

还有这个

```
<div style="display: flex; padding: 1rem; margin: 1rem;" /> 
```

Enter fullscreen mode Exit fullscreen mode

还有这个

```
<img style="border-radius: 50%;" src="cat.gif" /> 
```

Enter fullscreen mode Exit fullscreen mode

而是移到了完全不同的`UTILITY CLASSES`，这样我们的代码看起来就像这样！

```
<p class="font-weight-bold text-right" /> 
```

Enter fullscreen mode Exit fullscreen mode

还有这个

```
<div class="d-flex p-1 m-1" /> 
```

Enter fullscreen mode Exit fullscreen mode

还有这个

```
<img class="img-rounded" src="cat.gif" /> 
```

Enter fullscreen mode Exit fullscreen mode

我想亲自感谢你们中的每一个人，感谢你们努力清理网络，确保 HTML 和 CSS 不再混合