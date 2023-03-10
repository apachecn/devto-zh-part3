# 3 个 CSS 特性使样式更加简单

> 原文：<https://dev.to/marianyp/3-css-features-that-make-styling-easier-1hin>

对于一些人，可能是大多数人来说，CSS 可能是一种痛苦。然而，CSS 确实提供了许多有用的实用工具，让开发人员和网站设计人员的生活变得更加轻松。让我们开始，看看 CSS 提供的众多特性中的 3 个。

# 1。Flexbox 中心对齐

第一个特性处理页面布局。通常情况下，你会发现你有一些想放在中间的内容，而不是放在页面上或父页面内部。不过，长期以来，垂直和水平居中都是一种“笨拙”的方法，有时可能会反应迟钝。现在，借助 flexbox 及其提供的对齐功能，我们可以以一种非常优雅的方式做到这一点。

在我们想要居中的元素的父元素上，我们可以应用以下样式

 `display: flex;
justify-center: center;
align-items: center;` 

这将允许我把这个:

[![](img/0f6fe72aca093b23195e68af6912a7ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SE2JwLwr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh5.googleusercontent.com/LGwk_0ehE3_tQygCyobeCEVRjCt88dH53Pj-qj0MUhT2OvWoZwi6n9ynsqmkoHnGEfXJ-6a6k_v4JZTISNTs1v93__WeeII7YR-rjJYUbyvJ7NmS00ETX6Dr-XdbpIG0w6wSc4-C)

变成这样:

[![](img/cdc187a7ac16aa82406cca00d24ae9b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--znXiPtEV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh4.googleusercontent.com/qcJQkKhLPM7J_pThqMZAJul1HhzZA0kM7hoyfXHNdBo7jOi0oMaGs4-Jd9qx1gJWQ0FjxzWlFbqwEAMTCor2AuBcJYx3UDlmHH7O5tO9l0V_y8pR5l1wE_hgUBuP9NCDE1JUiepM)

## 这是怎么回事？

在 flexbox 中，您有两个轴。你有所谓的主轴和横轴。主轴本质上是一个 X 轴，它水平放置内容。相比之下，横轴就像 Y 轴，它会垂直放置内容。justify-center 属性和 align-items 属性将允许您分别更改项目的对齐方式。“对齐-中心”是主轴修改器，“对齐-项目”是横轴修改器。

```
<div class="my-container">  
  <div class="text">Hi, my name is Mariany 😅</div>  
</div> 
```

Enter fullscreen mode Exit fullscreen mode

所以我们告诉父元素(my-container)使用 flex 显示。为了能够使用对齐项目和居中对齐，我们需要对此进行定义。然后我们添加 justify-center，将文本移到中间。然后我们添加 align-items，它向下滑动文本。这将使我们的`my-container`样式看起来像这样:

```
.my-container {

  display: flex;  
  align-items: center;  
  justify-content: center;

  background: #ddc942;  
  width: 100vw;  
  height: 100vh;

} 
```

Enter fullscreen mode Exit fullscreen mode

我推荐在[CSS-flexbox 技巧指南](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)阅读更多关于 Flexbox 及其特性的内容

# 2。过渡

当制作像按钮或链接悬停这样的东西时，或者实际上任何与此相关的东西时，这被认为是过于简单了；使用动画和关键帧可能会过度。谢天谢地，我们有方便的过渡。当元素的某些属性改变时，转换允许我们转换元素。想想吧！只用 2 行 CSS 就制作了一个悬停动画！

我们可以用 transition 属性做到这一点。transition 属性最多可以取 4 个值，但最少取 2 个值。将转换添加到您想要定位的任何元素选择器中。例如，如果我想在一个元素被覆盖时过渡到它的背景，我可以这样做。

```
.box {  
  width: 300px;  
  height: 300px;  
  background: red;  

  transition: background 1s;  
}  

.box:hover {  
  background: blue;  
} 
```

Enter fullscreen mode Exit fullscreen mode

本质上，我们告诉 box 元素在背景改变时动画或过渡，而不是立即改变它。然后，我们添加一个选择器，每当用户将鼠标悬停在 box 元素上时，它就会变成粉红色。

您可以在 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/CSS/transition)中阅读更多关于过渡的信息。

# 3。calc()函数

知道 CSS 有函数你可能会很惊讶。的确如此。它们非常有用。在我看来最有用的是 calc()函数。calc 函数允许您在处理尺寸时使用两种不同类型的单位。例如，假设我想将一个框的边框半径设置为 30%，但我想增加 44px？如果没有这个函数，就需要进行大量的反复试验才能计算出确切的百分比。使用 calc()函数，我可以执行如下操作:

```
.my-box {  
  border-radius: calc(30% + 44px);  

  width: 400px;  
  height: 400px;  
  background: orange;  
} 
```

Enter fullscreen mode Exit fullscreen mode

calc 函数可以做加法、减法、乘法和除法。所以它给了你很多机会来制作“动态”风格的内容。它在处理 100vh 和导航栏时特别有用。

如果您想了解更多关于 calc 功能的信息，请阅读更多关于 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/CSS/calc)的内容。