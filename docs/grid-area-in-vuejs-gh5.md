# VueJS 中的网格区域

> 原文：<https://dev.to/mouseannoying/grid-area-in-vuejs-gh5>

正如你可能从最近的帖子中看到的，我大量使用了 CSS 网格布局...同时，我也不得不支持 IE11，哦，太好了。事情是这样的，我喜欢用网格区域的方法来定义布局中的东西应该放在哪里，我是这样写的:

```
{ 
  ... 
  grid-area: grid-row-start / grid-column-start / grid-row-end / grid-column-end; 
  ...
} 
```

这很酷，但我花了很长时间才明白为什么它不起作用...尽管它在 IE11 中也很简单，我用四个声明替换了它:

```
{ 
  ... 
  grid-row: grid-row-start; 
  grid-row-span: (grid-row-end - grid-row-start); 
  grid-column: grid-column-start; 
  grid-column-span: (grid-column-end - grid-column-start); 
  ...
} 
```

虽然它仍然没有像预期的那样工作，但是我发现 Vue 用 auto 替换了 grid-row-end 和 grid-column-end。然后我注意到 JS 语法略有不同，因为最后两个值也可以像这样使用跨度:

```
{ 
  ... 
  grid-area: grid-row-start / grid-column-start / span (grid-row-end - grid-row-start) / span (grid-column-end - grid-column-start); 
  ...
} 
```

将其设置为四个整数不起作用，将其设置为前缀为 span 的整数在 Vue 中起作用，其中最后两个整数被替换为 auto——所以我用宽度和深度跨度替换了所有实例，并相应地更新了我的脚本，现在需要的输出是:

```
{ 
  ... 
  grid-area: grid-row-start / grid-column-start / span width / span depth; 
  ...
} 
```

结合 IE11 所需的特异性，我们得到如下:

```
{ 
  ... 
  grid-row: grid-row-start; 
  grid-row-span: width; 
  grid-column: grid-column-start; 
  grid-column-span: depth; 
  grid-area: grid-row-start / grid-column-start / span width / span depth; 
  ...
} 
```

当转换为方法函数时，结果如下:

```
placeElement: function (x, y, width, depth) {
  return { 
    gridRow: x, 
    gridRowSpan: width, 
    gridColumn: y, 
    gridColumnSpan: depth, 
    gridArea: [x, y, "span " + width, "span " + depth].join(" / "),
  };
} 
```

看起来不太好，但是 Vue 会自动给单个值加上前缀，这样 gridRow 就变成了`-ms-grid-row`等等。对于 IE11，除了 Chrome 等人的网格区域之外，我不想输出任何东西。

因为我们有四个参数，所以它看起来更清晰，而且定义元素应该跨越多少行和多少列看起来更自然和更符合逻辑。

与此同时，返回一个对象意味着我们可以在随后的函数中添加它。