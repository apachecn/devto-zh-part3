# TIL:div 是定义列表中的有效元素

> 原文：<https://dev.to/stefanjudis/til-divs-are-valid-elements-inside-of-a-definition-list-4iik>

我是语义标记的忠实信徒，这就是为什么只要有意义，我就使用[描述列表](https://html.spec.whatwg.org/multipage/grouping-content.html#the-dl-element)。今天我在读`dl`元素的说明书，发现了一些我希望几年前就知道的东西。

我发现的规范中令人惊讶的例子如下:

```
<dl>
 <div>
  <dt> Last modified time </dt>
  <dd> 2004-12-23T23:33Z </dd>
 </div>
 <div>
  <dt> Recommended update interval </dt>
  <dd> 60s </dd>
 </div>
 <div>
  <dt> Authors </dt>
  <dt> Editors </dt>
  <dd> Robert Rothman </dd>
  <dd> Daniel Jackson </dd>
 </div>
</dl> 
```

Enter fullscreen mode Exit fullscreen mode

`div`元素允许在`dl`元素内部？什么？

该规范对其定义如下:

> 为了用微数据属性或者其他适用于整个组的全局属性来注释组，或者仅仅是出于样式的目的，dl 元素中的每个组都可以包装在 div 元素中。这不会改变 dl 元素的语义。

你可能会说，“好吧，斯特凡，怎么回事？”。如果你已经做了几年的 web 开发(在我们有 flexbox 和 grid 之前)，你可能知道描述列表的样式总是有点烦人。包含`div`元素的可能性使得应用基于行的样式更加容易。

那会节省我很多时间。我希望我 5 年前就知道了。:D

编辑:原来它并没有那么老。它被添加到 WHATWG 规范【2016 年 10 月并被做成 [HTML 5.2 W3C 推荐](https://www.w3.org/TR/html52/grouping-content.html#the-dl-element)。

如果你想了解更多关于允许`dl` [中的`div`元素的决定，这个 GitHub 问题](https://github.com/whatwg/html/issues/1937)会详细讨论。