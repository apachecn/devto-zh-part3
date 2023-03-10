# 使用自定义属性设置样式(主题化)

> 原文：<https://dev.to/vish448/styling-themeing-with-custom-properties-lgj>

## 默认主题

为默认主题或调色板创建变量。

```
 Color palette for our default theme.
[data-theme="default"] {
  --bg-color: #D0DCE9;
  --border-color: #FFFFFF;
  --header-bg-color: #3B71A6;
  --main-bg-color: #FFFFFF;
  --aside-1-bg-color: #82A4C6;
  --aside-2-bg-color: #0ebeff;
  --footer-bg-color: #96D5F1;
} 

```

现在我们可以将上面创建的默认主题变量应用到我们的组件中。

```
 body{
  background: var(--bg-color);
}
h1{
  color: var(--footer-bg-color);
}
header{
  background: var(--header-bg-color);
}
main{
  background: var(--main-bg-color);
}
.aside-1{
  background: var(--aside-1-bg-color);
}
.aside-2{
  background: var(--aside-2-bg-color);
}
footer{
  background: var(--footer-bg-color);
} 
```

将 data-theme 属性应用到我们的 html 主体，您会看到新的调色板是如何应用到主题的。我们可以根据需要添加更多的调色板，并根据需要改变颜色。

> data-theme = "glow "

## 我们来看个例子。

如果您更改了`<body>`元素中 data-theme 属性的值，您将看到应用了新的调色板。
[https://codepen.io/vishangsoni/embed/aMgJrO?height=600&default-tab=result&embed-version=2](https://codepen.io/vishangsoni/embed/aMgJrO?height=600&default-tab=result&embed-version=2)

## 担忧

我不确定它的扩展性如何，以及它是否符合您的要求。我相信如果你正在创建一个小网站，你需要改变主题颜色来达到营销的目的，这种方法可以节省一些时间。