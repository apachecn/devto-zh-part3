# SCSS 小抄

> 原文：<https://dev.to/finallynero/scss-cheatsheet-7g6>

### 什么是 SCSS

SCSS 的意思就是时髦的 CSS。Sassy CSS 是一个 CSS 预处理器，它可以让你使用普通 CSS 中没有的功能。在本文中，我将列举、解释并举例说明其中的一些特性。

#### 变量

在 SCSS，你可以很容易地声明变量。要创建一个变量，只需在变量名上添加一个`$`符号，并像普通的 CSS 属性一样设置它们。

```
// Font-weight for a project

$font-light : 400
$font-thick: 500
$font-heavy: 600

.element{
    Font-weight: $font-light;
}

//color palette for a project to maintain consistency

$product-dark-blue: #324e85
$product-light-blue:#4c7396
$product-lighter-blue:#9bb7cf

.element{
    color: $product-dark-blue;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 嵌套

SCSS 允许你嵌套 CSS 规则

```
// vanilla CSS

.container {
  Width: 100%;
  Color: grey;
  Background-color: green;
}
.container div {
  Border: 1px solid black;
}
.container div a {
  text-decoration: none;
  color: #f2f2f2;
}
.container div a::hover {
  color: #b2b2b2;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// SCSS

.container {
  width: 100%;
  color: grey;
  background-color: green;

  div {
    border: 1px solid black;

    a {
      text-decoration: none;
      color: #f2f2f2;
      &::hover {
        color: #b2b2b2;
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

时髦不是吗？SCSS 帮助你写干净简洁的 CSS。

#### [继承](#inheritanceusing-raw-extend-endraw-or-raw-mixin-endraw-)(使用`@extend`或`@mixin`)

###### @扩展

帮助你继承另一个类的属性。

```
 // SCSS
.header {
  color: grey;
}

.sub-header {
  @extend .header;
  font-size: 40px;
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是 SCSS 汇编到

```
 // Compiled CSS

.header, .sub-header {
  color: grey;
}

.sub-header {
  font-size: 40px;
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 米辛

Mixin 是 SCSS 使用`@mixin`实现继承的另一种方式。你可以用`mixin`达到和`@extend`一样的效果。首先使用`@mixin`创建 mixin，然后使用`@include`
将其添加到任何需要该属性的类中

```
// SCSS

//create mixin
@mixin red-color{
  color: grey;
}

.header{
  @include red-color;  /* add mixin */
}

.sub-header{
  @include red-color;
  font-size: 40px;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
.header {
  color: grey;
}

.sub-header {
  color: grey;
  font-size: 40px;
} 
```

Enter fullscreen mode Exit fullscreen mode

你能看出编译后的`@extend`的`css`和`@mixin`的不同吗，`@mixin`将样式放在两个选择器(标题和副标题)中，而`@extend`用逗号分隔两个选择器，然后将该样式应用于它们。

有一件事`@mixin`可以做而`@extend`不能做，那就是传递参数并使用它。`@mixin`参数也可以取默认值。

```
// SCSS

@mixin fontSize ($params: 10px){
  font-size: $params;
}

.header{
  @include fontSize(20px);
}

.sub-header{
  @include fontSize(20px);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Compiled CSS
.header {
  font-size: 20px;
}

.sub-header {
  font-size: 20px;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你需要继承一个类，你可以使用它们中的任何一个，但是根据 [CSS 技巧](https://css-tricks.com/the-extend-concept/)的最佳实践是当你不传递参数时使用@extend。

#### 导入

SCSS 允许您使用`@import`
将其他 SCSS 样式表导入 SCSS 文件

```
@import “button.scss” or @import “button” 
```

Enter fullscreen mode Exit fullscreen mode

#### 偏音

片段是 SCSS 文件，你不知道要编译成 CSS，但是你想把它们导入(使用`@import`)到另一个文件中。要创建一个部分，你只需要在文件名`_font.scss`的开头加一个下划线，然后你可以导入它们，带下划线也可以不带下划线。
Partials 有助于模块化你的代码和分离关注点。
例如，在我的项目中，我选择了颜色、字体、按钮等，然后将它们导入到 main.scss 文件中。

```
/* _colors.scss(partial) */

$light-gray: #F2F2F2
$dark-gray: #737373
/*EOF colors.scss*/

/* _buttons.scss(partial) */

.button-primary{
  color: #4c7396;
  background-color: #ffffff;
}
.button-secondary{
  background: #4c7396;
  color: #FFFFFF;
}
/*EOF buttons.scss*/

/* main.scss */
@import "_buttons.scss"
@import "_colors.scss" 
```

Enter fullscreen mode Exit fullscreen mode

#### 运算符

Scss 为您提供了不同种类的操作符，您可以在 css 中使用它们。算术运算符，如:

*   加法(+)
*   减法(-)
*   除法(/)
*   乘法(*)等等

```
 // SCSS
@mixin top-margin ($margin){
  margin-top: 30px + $margin;
}

.container{
  width: 800px - 80px;
  @include top-margin(10px);
}

//Compiled CSS

.container {
  width: 720px;
  margin-top: 40px;
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，算术运算符仅在两个值使用相同单位时有效，即`rem`、`em`、`px`

它们还支持比较运算符，如`==`、`!=`、`<`、`>`、`<=`以及逻辑运算符`and`、`or`、`not`。

您可以查看这篇文章以获得更多关于操作符的信息

[![sarah_chima](img/12559cb85172c679a639470250e41b1d.png)](/sarah_chima) [## SASS 运算符

### 莎拉奇马 11 月 24 日 175 分钟阅读

#sass #css #beginners](/sarah_chima/sass-operators-56f)

#### 颜色功能

Scss 提供了一些可用于处理颜色的功能。其中一些包括:

*   mix($colorX，$colorY，weight):这个函数用于混合两种颜色。第一个参数是第一种颜色，第二个参数是第二种颜色，第三个参数是您想要混合的第一种颜色的百分比。

```
mix(blue, grey, 30%) /*results 30% blue and 70% grey*/ 
```

Enter fullscreen mode Exit fullscreen mode

*   lighten($color，$amount):这个函数用于返回一个更亮的颜色。第一个参数是颜色，第二个参数是你想让它变亮的百分比。

```
lighten(#ff0000, 30 ) /*results #ff9999/* 
```

Enter fullscreen mode Exit fullscreen mode

*   变暗($color，$amount):采用与变亮函数相似的参数，但返回指定的更暗的颜色。

```
darken(#ff0000, 30 ) /*results #660000*/ 
```

Enter fullscreen mode Exit fullscreen mode

*   `opacify($color, $amount)`:该函数返回一个不透明度增加的颜色。第一个参数是颜色，第二个参数是介于 0 和 1 之间的值。

*   这个函数使颜色变得更加透明，它使用类似的参数来使颜色变得不透明。它返回一种不透明度降低的颜色。你可以说它是不透明的反义词。

这些只是 Scss 中可用的一些颜色功能，您可以查看[文档](https://sass-lang.com/documentation/file.SASS_REFERENCE.html)了解更多信息。

#### 其他功能

Scss 仍然提供其他可能对您有用的功能

*   `str-length($string)`:这个函数返回一个字符串中的字符数。
*   `percentage($number)`:此函数将没有单位的数字转换成百分比。
*   `round($number)`:该函数将一个数字四舍五入为最接近的整数。
*   `min($number1, $number2, $number3, …..)`:该函数返回一组数字中的最小值。
*   `random()`:返回一个随机数，不接受任何参数。
*   `quote($string)`:这个函数给字符串加引号。
*   `unquote($string)`:这个函数从字符串中移除引号。
*   `to-lower-case($string)`:这个函数将一个字符串转换成小写。
*   `to-upper-case($string)`:这个函数将一个字符串转换成大写。

#### 循环往复

多亏了 Scss，我们可以在 css 中使用`for`循环。这对于创建实用程序类非常有用。你可以为你的颜色、字体大小、边距、填充和许多其他属性创建类。

下面是一些我用来在我的项目
中创建工具类的循环的例子

```
/* Generate utility classes for font-size */

@for $x from 1 through 70 {
  .font-size-#{$x} {
    font-size: 0px + $x;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以如果我需要在一个元素上使用字体大小为 20 的类，我可以很容易地做到。

```
/* Generate utility classes for margin */

@for $i from 0 through 500 {
  .m#{$i} {
    margin: 0px + $i;
  }
  .mt#{$i} {
    margin-top: 0px + $i;
  }
  .mb#{$i} {
    margin-bottom: 0px + $i;
  }
  .ml#{$i} {
    margin-left: 0px + $i;
  }
  .mr#{$i} {
    margin-right: 0px + $i;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你在你的项目中使用了循环来创建工具类，请在评论中分享，这样其他人也可以采用它们。循环是我最喜欢的 scss 功能

#### 条件句

scss 的另一个令人惊叹的特性是能够在 css 中使用 If/else 语句。我还没有在我的任何项目中真正使用过这个功能，但我认为它们很棒。

```
$bg: pink;
$bg-mobile: red;

p {
  @if $bg == pink {
    color: blue;
  } @else if $bg-mobile == red {
    color: green;
  } @else {
    color: grey;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是 if else 语句的一个简单用例，如果你在一个项目中使用过它，请在评论中分享一个代码片段。

Scss 的特性比本文所介绍的要多得多，但这些都是常用的特性。你可以查看他们的[文档](https://sass-lang.com/documentation/file.SASS_REFERENCE.html)。