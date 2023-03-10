# LWC–第一眼–用 CSS 添加风格

> 原文：<https://dev.to/brettmn/lwc--first-look--adding-style-with-css-1j23>

[https://www.youtube.com/embed/ZYVIX9reVpE](https://www.youtube.com/embed/ZYVIX9reVpE)

> 我在尝试新的东西。下面是一个视频生成的文字记录，稍加编辑。如果有任何问题，请告诉我[Brett@WIPDeveloper.com](mailto:Brett@WIPDeveloper.com)

你好，我是 WIPDeveloper.com 的布雷特。由于 Salesforce Lightning 设计系统，我们的第一个组件现在有了样式。但有时你想得到更多的自定义，并使用自己的 CSS。

## 添加样式表

为此，我们必须在组件中添加一个 CSS 文件。所以在同一个目录下新建一个文件，打算把它叫做`firstComponent.css`。

由于 lightning Web 组件使用 Shadow DOM 来样式化我们的单个组件，所以我们将使用 CSS 伪类`:host`来仅在我们的组件中指定。让我们防止来自外部组件的样式渗入，这将缩小我们的样式范围，将背景颜色改为浅蓝色。我们必须使用`:host`并在我们的 CSS 选择器中应用来获取 CSS 类，这将是一个`slds-page-header`。

像处理普通样式表一样分配 CSS 样式。现在我们可以部署它了。

刷新几次，现在我们有了一个浅蓝色的背景，我们可能希望当我们悬停在它上面时也有一个效果。因此，要添加悬停效果，您可以再次使用主机，除了这次使用括号

并传递 hover 伪类。这是用于类名的 CSS 选择器，这次我们将背景色指定为白色。

现在，当我们悬停在它上面时，我们应该看到背景从浅蓝色变成白色。

部署完成后，再刷新几次。

现在当我们悬停时，它从白色浅蓝色变成白色。因此，如果我们想针对一个特定的元素，如是真棒，我们可以使用 CSS 类。

在段落标签上。

所以我们仍然需要宿主

把颜色改成蓝色。部署它，现在我们的目标只是一个特定的元素，而不是我们整个客户的整个元素。所以这也是可能的。

已部署。恢复精神

那里，现在，是令人敬畏的是蓝色的，我们盘旋着变成白色

这就是当你来到一个标题中有 developer 的网站来获得样式建议时会发生的情况，蓝上蓝，也许不是最好的计划。

#### 最终样式

```
:host .slds-page-header {
  background-color: lightblue;
}

:host(:hover) .slds-page-header {
  background-color: white;
}

:host p.slds-page-header__name-meta {
  color: blue;
} 
```

## 链接

*   [:MDN 上的主机()](https://developer.mozilla.org/en-US/docs/Web/CSS/:host())
*   [使用阴影 DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM)

## 现在就这样。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

帖子[WIPDeveloper.com](https://wipdeveloper.com/lwc-first-look-adding-style-with-css/)最先出现在[上【LWC——第一眼——用 CSS 添加样式](https://wipdeveloper.com)。