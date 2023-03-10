# Sass : @function，@mixin，placeholder，@extend

> 原文：<https://dev.to/keinchy/sass--function-mixin-placeholder-extend-18g6>

在最近的一个项目中，我有机会创建一个定制的框架，在处理结构、添加功能、创建 mixins 时，我决定与我的开发伙伴分享一些信息。

我们开始吧...函数是返回任何 Sass 数据类型的单个值的代码块。
下面的函数是我经常使用的一个，它取一个像素大小，转换成 rem。

```
@function calculate-rem($size) {
  $rem-size: $size / 16px;
  @return #{$rem-size}rem;
}
*I could easily just use numbers but
people in the past have had the 
habit of using px for all of their font sizes. 
```

Enter fullscreen mode Exit fullscreen mode

@mixin，非常类似于函数，但两者之间的主要区别是 mixin 输出将直接编译成 CSS 样式的 Sass 代码行，而函数返回一个值，然后该值可以成为 CSS 属性的值，或者成为可能传递给另一个函数或 mixin 的值。

```
@mixin font-size($size) {
  font-size: calculate-rem($size);
}
mixin is taking $size as an argument to pass into
the calculate-rem function to evaluate into rems 
```

Enter fullscreen mode Exit fullscreen mode

使用 mixin 的时候你需要这样使用:`@include font-size(16px)`，这个会编译成`font-size: 1rem;`

或者，我可以在我的类中直接使用这个函数:

```
.my-class {
    font-size: calculate-rem(16px);
} 
```

Enter fullscreen mode Exit fullscreen mode

占位符非常类似于类选择器，但是它没有使用句点(。)开始时，使用百分比字符(%)。占位符选择器有一个额外的属性，即它们不会出现在生成的 CSS 中，只有扩展它们的选择器才会包含在输出中。

```
%icon {
  transition: background-color ease .2s;
  margin: 0 .5em;
} 
```

Enter fullscreen mode Exit fullscreen mode

@extend 允许类之间共享一组属性。@extend 一个类的选择器将它们的选择器包含在它正在扩展的类的右边，产生一个逗号分隔的列表。

```
.foo {
  color: black;
  border: 1px solid black;
}

.bar {
  @extend .foo;
  background-color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

以上编译为:

```
.foo, .bar {
  color: black;
  border: 1px solid black;
}

.bar {
  background-color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

占位符应该与@extend 一起使用，所以通常如果您知道一组属性将在一些元素中使用，您可以创建一个占位符并扩展属性。

```
%icon {
  transition: background-color ease .2s;
  margin: 0 .5em;
}
.foo {
  @extend %icon;
  color: black;
  border: 1px solid black;
}

.bar {
  @extend %icon;
  background-color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成:

```
.foo, .bar {
  transition: background-color ease .2s;
  margin: 0 .5em;
}

.foo{
  color: black;
  border: 1px solid black;
}

.bar {
  background-color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是对这些 Sass 特性的快速解释，如果有足够的请求，我可以用更高级的混合和逻辑做第二部分。我希望你能发现这是有用的，如果你有任何问题，请在评论中或直接联系我们。

-乐意帮忙。