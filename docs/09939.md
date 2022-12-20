# 使用 css 变量创建流畅的动画

> 原文：<https://dev.to/cydstumpel/using-css-variables-to-create-smooth-animations-5164>

用 JavaScript 创建动画真的很令人沮丧。您实际上还不能直接从 JavaScript ( [)操纵 CSS 对象模型！](https://ishoudinireadyyet.com/))，但你肯定想...

今天大多数人使用 JavaScript 的`classList`或`style`属性来改变样式。两者的问题是它们实际上都被添加到 HTML DOM 中，而不是直接添加到 CSS 引擎中。等待胡迪尼被广泛支持可能需要一段时间，我们现在想创造更流畅更好的动画。
T3![Dumbledore shruggs his shoulders](img/19ef082ded1541817fb9911c003d65d7.png)T5】

## 那么，我们能做什么呢？

答案是 CSS 变量，或者有些人喜欢称之为 CSS 自定义属性。因为虽然 CSS 变量也是通过 JavaScript 中的`style`属性来操作的，但在大多数浏览器中，它实际上比使用内联样式要快得多。

使用事件监听器来改变变量是疯狂的顺利，这里有一个例子，我写了几个星期前(使用可变字体，这也是 v .酷！):

[https://codepen.io/Sidstumple/embed/wOJgjd?height=600&default-tab=result&embed-version=2](https://codepen.io/Sidstumple/embed/wOJgjd?height=600&default-tab=result&embed-version=2)

CSS 变量的另一大优势是你可以在父元素上设置变量，所有子元素都可以读取，随后还可以设置`:before` & `:after`伪选择器。当有很多子元素时，这确实会使添加新变量的速度变慢！

尤其是基于鼠标位置的动画在我看来非常好:
[https://codepen.io/Sidstumple/embed/jJaWBr?height=600&default-tab=result&embed-version=2](https://codepen.io/Sidstumple/embed/jJaWBr?height=600&default-tab=result&embed-version=2)

## 但是 IE 呢？

所以大多数浏览器都支持 CSS 变量，当然除了...即。但是说真的，甚至微软也在告诉人们停止使用 IE11。只要你只是为了增强而使用动画，你的用户——甚至那些用 IE 折磨自己的人——应该没问题。