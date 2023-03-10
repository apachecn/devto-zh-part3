# 一天之内的推特自举

> 原文：<https://dev.to/kauresss/twitter-bootstrap-in-a-day-1j2j>

## 简介

*注:正在进行的工作*

因此，你必须在 30 分钟内为一个可以在笔记本电脑、手机和平板电脑上扩展的人编写/制作一个网站。你是做什么的？：

1.  连夜编写自己的前端库
2.  使用 Twitter 引导

答案是你使用 Twitter Bootstrap，因为你只有 30 分钟的午休时间。因为你为什么要浪费你的午休时间编写一个前端框架:P

## 响应式设计

响应式设计是指让网页/网站的前端根据所浏览的屏幕的高度和宽度进行缩放。伊桑·马科特在《一份清单之外》中首先描述了响应式设计。

为了使网页具有响应性，我们使用 meta 标签:

```
 <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
```

Enter fullscreen mode Exit fullscreen mode

这基本上意味着你告诉浏览器视窗的宽度[也就是你正在使用的设备的屏幕尺寸]应该有一个 1 的初始缩放比例。Device-width 是以 CSS 像素为单位的屏幕宽度，比例为 100%。

使元素具有响应性的另一种方法是使用百分比，而不是固定的宽度和高度。例如 100%、50%等。这将把元素缩小到可用屏幕宽度和高度的 100%或 50%。例如:

```
img{
width: 100%;
height: auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 进入推特自举 v.4

因为你只有 30 分钟的午休时间，所以花时间让你的网页上的每一个元素都有反应会适得其反。这意味着您需要将大量的类和 id 附加到您的元素上，所以让我们了解一下关于 bootstrap 的最重要的事情..

## 网格系统

所以你想让东西根据屏幕宽度的大小来缩放，你该怎么做呢？网格系统。

Bootstrap 的网格系统如下:

[![](img/a43cb633d9b5ce090409349492d665c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VC4pPItY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5je72s7u1thx8oj9vwam.png)

(图片取自 W3 学校)

所以上图意味着一个页面可以有 12 个“div/span”列和任意数量的行。为什么会这样呢？因为你可以向下滚动一页，但不能增加设备屏幕的宽度(比如你不能让你的 iphone 更宽)。

好吧，那么什么是争吵呢？良好的..

[https://codepen.io/Kauress/embed/YMVKqb?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/YMVKqb?height=600&default-tab=result&embed-version=2)

如你所见，行中有列。

## 综合起来:第 1 部分

你如何把它和你的 HTML 联系起来，然后真正开始使用 Twitter Bootstrap？

到目前为止，你必须习惯于为你的 div 定制类和 id，然后使用 CSS 对它们进行样式化和定位。

要使用 Bootstrap，你必须使用他们的自定义类，这些类基于上面的网格系统(一行 12 列)。

所以基本上你的“中心-这个-标题-现在”类的元素变成了这个:

```
< div class=" row justify-center">Hello</div 
```

Enter fullscreen mode Exit fullscreen mode

## 综合起来:第 2 部分

**集装箱集装箱集装箱**

你不希望你所有的东西都漂浮在

of your document. It messes up with everyone's head.

你的元素(任何标题，p 的，图像等)必须在容器内。

那么如何制作一个容器呢？您可能习惯于像这样制作自己的容器分区:

```
<div class="main-div">
<h4>My life story</h4>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在 bootstrap 中，您必须使用 *class="container"* ，因为请记住:
**要使用 Bootstrap，您必须使用它们的自定义类，这些自定义类基于上面的网格系统(一行 12 列)。**

这就是 Bootstrap 的情况:

```
<div class="container">
<h4>My life story</h4>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这就是你最终得到的结果:

[https://codepen.io/Kauress/embed/wZdvwr?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/wZdvwr?height=600&default-tab=result&embed-version=2)

你的“main-div”也可以是一个容器，它的宽度是整个屏幕的宽度，有一个名为“container-fluid”的类:

[https://codepen.io/Kauress/embed/zXwxvK?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/zXwxvK?height=600&default-tab=result&embed-version=2)

**把你的元素放在容器里可以让它们更容易定位**

 **## 把所有的放在一起:第 3 部分

**容器**包含**行**。为了使用 Bootstrap 的网格系统，你必须以他们的方式使用他们的系统。所以在一个容器中你会有一个带有 class = "row "的 div。这类似于你自己的 HTML，你可能有这样的东西:

```
<div class="main-div">
  <div class="heading-div">
<h4>My life story</h4>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这现在变成:

```
<div class="container">
  <div class="row">
<h4>My life story</h4>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

**一行横跨整个集装箱的宽度。**

[https://codepen.io/Kauress/embed/BERyzM?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/BERyzM?height=600&default-tab=result&embed-version=2)

## 把所有的放在一起:第 4 部分

请记住，每行有 12 列，这种将行划分为列的方式，以及将一行放入一个容器中是使元素具有响应性的原因。

上面的代码可以这样重构:

```
<div class="container">
  <div class="row">
<div class="col-sm-12">
<h4>My life story</h4>
   </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在下面的代码笔中，有一个跨越 12 列的“sm”(small)“div/span”，这是该容器中整个行的宽度。

[https://codepen.io/Kauress/embed/OGmPmq?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/OGmPmq?height=600&default-tab=result&embed-version=2)

正如我提到的，一行中可以有 12 列，所以假设我们想要两个 div 并排，如下所示:

```
<div class="container">
  <div class="row">
<div class="col-sm-6">
<h4>My life story</h4>
   </div>
<div class="col-sm-6">
<h4>My life story again</h4>
   </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/Kauress/embed/MRmYQj?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/MRmYQj?height=600&default-tab=result&embed-version=2)

*注意*电网系统有 5 个等级:

| 班级 | 屏幕大小 |
| --- | --- |
| 。列 xs | Xtra small 适用于小于 576 像素的屏幕 |
| 。颜色-颜色 | 小屏幕> = 576 像素 |
| 。col-md | 中等屏幕> = 768 像素 |
| 。col-lg | 大屏幕> = 992 像素 |
| 。列 xl | 屏幕尺寸> = 1200 像素 |

因为每个类都将向上扩展，所以您只需要指定两个类中较小的一个。比如说。col-sm-6 将扩大到。列-md-6

## 带自举的布局

为了更好地理解这一点，我们来看几个布局..

1.  布置等宽的 5 列

对于等宽的 div(即列),只需使用。没有任何数字。Bootstrap 会自动将行中的列等宽分开

[https://codepen.io/Kauress/embed/oOWVjN?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/oOWVjN?height=600&default-tab=result&embed-version=2)

## 列内列外

是的，你可以把一列分成几列。例如，下面一行有一个 col-sm-6 和两个 col-sm-3，总共有 12 个。

col-sm-6 可以有嵌套列:

[https://codepen.io/Kauress/embed/MRmRGy?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/MRmRGy?height=600&default-tab=result&embed-version=2)

1.  Clearfix 类

在 CSS 中使用 *float* 属性是不可取的，太麻烦了。如果您碰巧使用了 float，那么在父元素上使用 clearfix 类。“clearfix”所做的基本上是“修复”任何由 float 带来的破坏，方法是强制容器位于已经被 float 的元素之下，并在它们之下呈现。

[https://codepen.io/Kauress/embed/VNWZOZ?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/VNWZOZ?height=600&default-tab=result&embed-version=2)

1.  抵消

通过将*偏移量*添加到列类中，您可以将该列向右移动特定数量的列。

例如

```
<div class="col-md-6 col-sm-offset-3">

</div> 
```

Enter fullscreen mode Exit fullscreen mode

这将导致在中型设备上 col-md-6 元素的左侧有 3 列(因此，左边距增加了 3 列)。

[https://codepen.io/Kauress/embed/YMQzMV?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/YMQzMV?height=600&default-tab=result&embed-version=2)

## 媒体查询

您可以在某些媒体断点处更改布局，如下所示

```
@media(min-width:576px){

}
@media(min-width:768px){

}
@media(min-width:992px){

}
@media(min-width:1200px){

} 
```

Enter fullscreen mode Exit fullscreen mode

根据 Bootstrap 的文档，超小型设备(纵向电话，小于 576px)不需要任何媒体查询，因为这是 Bootstrap 中的默认设置。

[https://codepen.io/Kauress/embed/OGgNjP?height=600&default-tab=result&embed-version=2](https://codepen.io/Kauress/embed/OGgNjP?height=600&default-tab=result&embed-version=2)

## 送上更

Bootstrap 是它的第四个版本，这意味着，他们增加了新的东西。**