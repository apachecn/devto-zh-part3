# 铸造🍃:组件

> 原文：<https://dev.to/gdotdesign/components-in-mint-4a4n>

*在[之前的帖子](https://dev.to/gdotdesign/getting-started-with-mint-3k2o)中，我向你展示了如何开始使用[薄荷](https://www.mint-lang.com)。在这篇文章中，我将向你展示如何使用组件:)*

组件是应用程序的主要组成部分，因为它们提供组合，可以有自己的状态，自己的风格，连接到一个商店来使用一些全局状态等等...

如果你熟悉 React 中的[类组件，你会觉得很熟悉。](https://reactjs.org/docs/react-component.html)

## 定义一个组件

组件的最小定义如下:

```
component MyComponent {
  fun render : Html {
    <div>
      "Hello World"
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`component`关键字用于定义一个组件(名称必须以大写字母开头)。

在组件的主体中，必须定义一个`render`函数(如果它不存在，您将得到一个很好的编译器错误),该函数必须返回 HTML 或一个字符串或一个数组(类型应该匹配`Html`、`String`、`Array(Html)`或`Array(String)`中的一个)

*你可能注意到没有`return`关键字，在 Mint 中一切都是表达式，就像隐式返回的函数体。*

## 作文

现在我们有了我们想要使用的组件，所以我们将把它添加到`Main`组件中(它是由应用程序呈现的组件，你可以把它看作是**根组件**)。

```
component Main {
  fun render : Html {
    <MyComponent/>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，它类似于一个 HTML 标签，但不是小写标签，而是使用组件的名称。

由于`Main`是一个组件，这表明组件可以相互组合，甚至可以递归组合。

*如你所见，组件是**自结束**，这意味着它没有结束标签，它可以有结束标签，但如果它没有子组件，就没有必要了。*

## 属性

就像 HTML 标签一样，组件也可以有属性(或者属性，如果你更喜欢这个词的话)。

属性可以在组件体中用关键字`property`:
定义

```
component MyComponent {
  property target : String = "Word"

  fun render : Html {
   <div>
     "Hello "
     <{ target }>
   </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

属性必须有一个**类型**和一个**默认值**这是因为编译器在使用它们时可以帮助你。

您可以用名称引用组件内部的属性，就像在 HTML 表达式中的`div` ( `<{ ... }>`是一个 HTML 表达式)。

您可以像处理 HTML 属性一样将值从父组件传递给组件(字符串可以相同，其他值使用括号)

```
component Main {
  fun render : Html {
    <div>
      <MyComponent target="Joe"/>
      <MyComponent target={"Bill"}/>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 样式

一个组件可以有自己的使用 CSS 编写的风格，并且可以附加到 HTML 标签上:

```
component MyComponent {
  property target : String = "Word"

  style target {
   font-style: italic;
   font-weight: bold;
  }

  fun render : Html {
   <div>
     "Hello "
     <span::target>
       <{ target }>
     </span>
   </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

样式由关键字`style`及其标识符定义。该标识符可用于使用`::`符号将样式附加到元素上。

在上面的例子中，我们将名为`target`的样式附加到包含目标属性的 span 中。

本帖到此为止，感谢阅读🙏

* * *

如果你想了解更多关于 Mint 的知识，请查看[指南](https://guide.mint-lang.com)📖

在下一部分中，我将展示如何处理组件内部的事件和状态😉在那里见👋