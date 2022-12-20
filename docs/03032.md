# 铸造🍃:样式元素

> 原文：<https://dev.to/gdotdesign/mint-styling-elements-295o>

*这是展示 Mint 特性的系列文章的第五篇，你可以在这里找到之前的文章:*

*   *[薄荷🍃:入门](https://dev.to/gdotdesign/getting-started-with-mint-3k2o)T3】*
*   *[薄荷🍃:组件](https://dev.to/gdotdesign/components-in-mint-4a4n)T3】*
*   *[薄荷🍃:组件的事件和状态](https://dev.to/gdotdesign/mint-events-and-state-of-components-3j3a)*
*   *[薄荷🍃:处理 HTTP 请求](https://dev.to/gdotdesign/mint-handling-http-requests-2ep3)T3】*

在这篇文章中，我将向你展示如何设计元素的样式。

* * *

在 Mint 中，样式是内置的，并在语言级别进行处理。

您可以使用`style`关键字:
向组件中的 HTML 元素添加样式

```
component Main {
  style base {
    /* You can use standard CSS here... */
    background-color: red;
    color: white;
  }

  fun render : Html {
    <div::base>
      "I'm white on red!"

      <span>"Yey!"</span>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`base`是样式的标识符，可用于将其附加到 HTML 元素，在开始标记(`<div::base>`)的名称后有两个冒号(`::`)。

在这个例子中，我们将样式`base`添加到了 div 中。

## 伪类和伪元素

在很多情况下，您需要对元素状态(`:hover`、`:active`等)进行样式化...)或者是伪元素(`::before`和`::after`)。

你可以通过添加一个带有`&`前缀的新块(就像 [sass](https://sass-lang.com/documentation/style-rules/parent-selector) 中的父选择器一样，但有限制):

```
style base {
  background-color: red;
  color: white;

  &::before {
    content: "Hello I'm blue!";
    color: blue;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样的语法也可以用来样式化子元素:

```
style base {
  background-color: red;
  color: white;

  & span {
    font-weight: bold;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 媒体查询

Mint 支持样式块中的媒体查询(具有相同的语法)以允许响应设计:

```
style base {
  background-color: red;
  color: white;

  @media (max-width: 600px) {
    font-style: italic;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 插值

在一个交互式应用程序中，你想根据某种状态来设计样式。在 Mint 中，你可以在 CSS 值中使用插值`{...}`来从组件中获取数据:

```
component Main {
  state color : String = "red"

  style base {
    color: {color};
  }

  fun handleClick : Promise(Never, Void) {
    if (color == "red") {
      next { color = "blue" }
    } else {
      next { color = "red" }
    }
  }

  fun render : Html {
    <div::base onClick={handleClick}>
      <{ "I'm " + color + "!" }>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们使用`color`状态来设置元素的样式，当点击它时，我们在`red`和`blue`之间切换。

可以在同一个值中使用多个插值，例如，如果我有一个`top`和一个`left`状态，我们可以使用它们来设置`transform`属性:

```
...
state left : Number = 100
state top : Number = 100

style base {
  transform: translate({left}px, {top}px) /* translate(100px, 100px) */
} 
```

Enter fullscreen mode Exit fullscreen mode

今天就到这里，谢谢你的阅读🙏：

* * *

如果你想了解更多关于 Mint 的知识，请查看[指南](https://guide.mint-lang.com)📖

在下一部分，我将向你展示你如何[创建一个可以与其他人共享的包](https://dev.to/gdotdesign/mint-creating-a-packages-5e9b)😉在那里见👋