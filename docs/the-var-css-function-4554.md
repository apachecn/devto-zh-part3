# Var() css 函数

> 原文：<https://dev.to/vish448/the-var-css-function-4554>

var() CSS 函数可用于插入自定义属性(有时称为“CSS 变量”)的值，而不是另一个属性的值的任何部分。

> var( - header-color，灰色)；

# 语法

> var(自定义属性名称，声明值)

该函数的第一个参数是要替换的自定义属性的名称，该函数的可选第二个参数用作后备值。如果第一个参数引用的自定义属性无效，函数将使用第二个值。

custom-property-name:由以两个破折号开头的标识符表示。自定义属性仅供作者和用户使用。

declaration-value:自定义属性的后备值。在自定义属性在用户上下文中无效的情况下使用。

# 看看 codepen 的例子

[https://codepen.io/vishangsoni/embed/aMPPjE?height=600&default-tab=result&embed-version=2](https://codepen.io/vishangsoni/embed/aMPPjE?height=600&default-tab=result&embed-version=2)

### 浏览器兼容性

[![browser compatibility](img/f8029e158f1ba2d60adbe39f7ceebddd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rt1xfvKQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ny1rbibe131m11x4222s.png)