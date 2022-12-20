# CSS3 网格布局 10 分钟指南

> 原文：<https://dev.to/kauresss/10-min-guide-to-css3-grid-layout-3nk4>

## 简介

CSS3 网格布局非常适合控制包含行**和列**的布局！

1.  创建布局结构(即行数和列数)
2.  在布局上放置项目(手动或通过自动放置)

继续前进

## 入门

1.  将项目放置在网格上:“显示:网格”

[https://codepen.io/Kauress/embed/WWELoK?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/WWELoK?height=600&default-tab=result&embed-version=2)

##设置网格列和行:

```
 grid-template-columns: 1fr 1fr 1fr 1fr;
 grid-template-rows: auto; 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/Kauress/embed/qwXGQK?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/qwXGQK?height=600&default-tab=result&embed-version=2)

*注*
1。网格-模板-列和行帮助定义*网格-轨迹*，它是二维网格上两条线之间的空间。

2.Fr: fr 是一个新的单位，代表分数。因此，我们使用每列可用空间的 1/4。

## 设置自动网格行/列

你可以用网格-模板-列/行明确地说明你想要多少列和多少行，或者你可以用*自动*
使用隐含的行数/列数

```
/* create as many rows as needed that are 100px in height*/
 grid-auto-rows: 100px; 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/Kauress/embed/OGxRQQ?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/OGxRQQ?height=600&default-tab=result&embed-version=2)

## minmax 函数

这是一个内置函数，有两个参数。第一个选项让您指定最小“像素”，第二个选项让您指定最大值(例如，auto 的最大值)。例如:

```
 grid-auto-rows: minmax(100px, auto); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/Kauress/embed/xeXqeE?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/xeXqeE?height=600&default-tab=result&embed-version=2)

## 定位物品

当您在网格上放置项目时，您的目标是线条而不是轨道。

```
grid-column-start, grid-column-end, grid-row-start and grid-row-end 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/Kauress/embed/yrzbgN?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/yrzbgN?height=600&default-tab=result&embed-version=2)

## 网格区域

当网格单元跨越多列或多行时，称为*网格区域*

## 网格檐槽

你可以用主网格容器上使用的*网格-列-间隙和网格-行-间隙*在列和行之间创建空间，例如:

```
.container {
 display: grid;
 grid-template-columns: 1fr 1fr 1fr 1fr;
 grid-auto-rows: minmax(50,auto);
 grid-column-gap: 20px;
 grid-row-gap: 20px;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/Kauress/embed/jRGmKb?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/jRGmKb?height=600&default-tab=result&embed-version=2)

## 嵌套网格

是的，你可以在主容器的网格元素中嵌套一个网格。简单设置*显示:网格*并指定列数和行数。例如:

[https://codepen.io/Kauress/embed/PgJmBg?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/PgJmBg?height=600&default-tab=result&embed-version=2)

## 对齐内容

1.  将元素沿行对齐*对齐-项目:居中*
2.  将列中的元素与*对齐-项目:居中*
3.  要对齐内容，请使用*文本对齐:居中*

[https://codepen.io/Kauress/embed/OGxgyP?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/OGxgyP?height=600&default-tab=result&embed-version=2)