# 设计有机 ui 组件的样式

> 原文：<https://dev.to/orgenicteam/styling-orgenic-ui-components-5g91>

# 设计有机 UI 组件的样式

ORGENIC UI 是一个不断发展的 web 组件集，专注于 UX 和设计。尽管 ORGENIC UI 附带了一个集成的主题系统，但为了个人目的快速有效地修改当前主题通常是有用的，而不是从头开始创建一个完整的主题。这个教程展示了如何改变 ORGENIC UI 标签组件的给定样式。

# 插入构件

只需添加以下代码即可在 ORGENIC UI 项目中使用选项卡控件:

```
<og-tab-container>
    <og-tab label="Tab Title">
        <!--CONTENT OF THE TAB -->
    </og-tab>
</og-tab-container> 
```

清单 1:将选项卡控件添加到模板中

[https://codepen.io/orgenic-team/embed/rgMzMB?height=600&default-tab=result&embed-version=2](https://codepen.io/orgenic-team/embed/rgMzMB?height=600&default-tab=result&embed-version=2)

# 样式组件

因为所有的 ORGENIC UI 组件都是带有 *ShadowDOM* 的本地 *WebComponents* ，所以不可能用基本的 CSS 特性覆盖它们。例如，无法像这样设置当前选定选项卡的背景色:

```
.og-tabs__list-item--selected {
    background: red;
} 
```

清单 2:这不起作用

## 自定义属性来救援

主题性是 ORGENIC UI 的基本特性之一。为了提供通过外部样式表在 ShadowDOM 中修改样式的能力，ORGENIC UI 使用 CSS **自定义属性**也就是 CSS 变量。因此，每个组件都有自己的 CSS 变量集，用户可以使用它们来创建个性化的设计。

所有用户界面组件及其自定义属性都记录在[http://docs.orgenic.org](http://docs.orgenic.org)

## 修改设计

这个例子将展示如何为标签应用新的背景颜色。文档提供了所需的 CSS 变量:

```
--og-tabs__tab-Background
--og-tabs__tab-Background--hover
--og-tabs__tab-Background--active 
```

只需创建一个新的 css 文件(custom-theme.css ),将新值应用于 CSS 变量，并将其作为最后一个样式表包含到 HTML 中:

```
og-tab-container {
    --og-tabs__tab-Background:                 #39b6e4;
    --og-tabs__tab-Background--active:         #1ca2d2;
    --og-tabs__tab-Background--hover:          #1ca2d2;
    color:                                     #FFFFFF;              
}

og-tab {
    color:      #333333;
} 
```

清单 3:覆盖默认主题

选项卡标题和内容的文本颜色设置为`inherit`。这意味着——尽管有 shadow DOM——设置 parents CSS 属性`color`来改变它就足够了，不需要任何特殊的自定义属性。

[https://codepen.io/jfuhrmann/embed/QROGjN?height=600&default-tab=result&embed-version=2](https://codepen.io/jfuhrmann/embed/QROGjN?height=600&default-tab=result&embed-version=2)

# TL；速度三角形定位法(dead reckoning)

借助预定义 CSS 变量的力量，修改 ORGENIC UI 组件的设计而不创建完整的主题是非常容易的。ShadowDOM 附带的封装使组件免受外来 CSS 的副作用，但也对自己特定的自定义属性开放。

# 请访问我们:

*   [https://orgenic.org](https://orgenic.org/?utm_source=devto&utm_medium=article&utm_campaign=stylingcomponents)
*   [https://github.com/orgenic/orgenic-ui](https://github.com/orgenic/orgenic-ui)
*   [https://twitter.com/orgenicUI](https://twitter.com/orgenicUI)