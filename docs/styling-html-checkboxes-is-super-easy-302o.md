# CSS 复选框样式 HTML 复选框是超级容易的

> 原文：<https://dev.to/proticm/styling-html-checkboxes-is-super-easy-302o>

> 最初发布于[我的博客](https://devinduct.com/blogpost/16/styling-html-checkboxes-is-super-easy)

## 古老的故事

过去，像`checkbox`这样的 HTML 组件很难按照我们想要的方式来设计样式。通常这些组件脱离了整体页面设计，这对用户体验来说不是很好。

更不用说每个浏览器都有(并且仍然有)自己的控件外观，从而导致了不同浏览器之间的外观差异。

为了克服这些情况，开发人员过去做了大量的黑客工作，比如隐藏输入，为复选标记创建图像和图标，以及添加一堆 JavaScript 代码来处理复选/取消复选。如果你问我，我会说这并不漂亮，而且看起来要做很多工作才能实现一些简单的事情。

## 新故事

上面描述的时代早已过去，我们比以往任何时候都更接近于以一种方式设计复选框的通用方式，当它们在所有浏览器上看起来和感觉都一样时，尤其是有消息称微软正在开发基于 chromium 的浏览器。你可以在这里阅读[。](https://www.windowscentral.com/microsoft-building-chromium-powered-web-browser-windows-10)

从我的角度来看，新的故事是，我们可以设计复选框的样式，而不隐藏它，也不添加 SVG 图像和 JavaScript 代码。这可以通过使用以下代码来实现:

1.  CSS `appearance`属性
2.  HTML `check mark`符号(_ 1000)

### 外观属性

> appearance 属性用于根据用户操作系统的主题，使用平台本机样式显示元素。

该属性支持许多值，但我们感兴趣的是值`none`。基本上，我们希望删除所有的本地样式，并应用自定义样式。最后，我们的复选框将会有好看的颜色和过渡，最重要的是，它在所有主流浏览器中的外观和感觉都是一样的。

用法示例:

```
.my-class {
   -webkit-appearance: value;
   -moz-appearance: value;
   /* -o-appearance: value; - Not required since the new version of Opera uses - 
      webkit prefix for this property, but we've added it nevertheless just to be aware 
      of it 
    */
   appearance: value;
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，让我们深入研究代码。

## HTML

我们的 HTML 标记非常简单。我们用一个`label`包装我们的`input`和一个`span`来保存文本。看起来是这样的:

```
<label class="checkbox">
    <input type="checkbox" />
    <span>Check Me</span>
</label> 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么特别的。我们使用了一个包装器元素来使内部项目更容易垂直对齐。这是通过 flexbox 布局完成的，我们将在 CSS 部分看到。

## CSS

CSS 样式看起来像这样:

```
.checkbox {
    display: inline-flex;
    cursor: pointer;
    position: relative;
}

.checkbox > span {
    color: #34495E;
    padding: 0.5rem 0.25rem;
}

.checkbox > input {
    height: 25px;
    width: 25px;
    -webkit-appearance: none;
    -moz-appearance: none;
    -o-appearance: none;
    appearance: none;
    border: 1px solid #34495E;
    border-radius: 4px;
    outline: none;
    transition-duration: 0.3s;
    background-color: #41B883;
    cursor: pointer;
  }

.checkbox > input:checked {
    border: 1px solid #41B883;
    background-color: #34495E;
}

.checkbox > input:checked + span::before {
    content: '\2713';
    display: block;
    text-align: center;
    color: #41B883;
    position: absolute;
    left: 0.7rem;
    top: 0.2rem;
}

.checkbox > input:active {
    border: 2px solid #34495E;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你认为这仍然是大量的 CSS，让我提醒你，我们不需要 flexbox 布局或转换来实现这种风格。添加这个是为了让它更优雅。如果我们移除多余的 CSS，我们需要做的就是通过设置`appearance`到`none`来移除默认样式，添加边框和颜色，并设置 HTML 符号。

让我们分解重要的部分来支持上面的陈述。第一步是使用`appearance`属性并删除默认样式:

```
...

-webkit-appearance: none;
-moz-appearance: none;
-o-appearance: none;
appearance: none;

... 
```

Enter fullscreen mode Exit fullscreen mode

希望这个属性很快成为一个标准，我们可以不用浏览器特有的前缀来使用它。

接下来，我们需要提供我们的自定义边框和背景:

```
...

border: 1px solid #34495E;
border-radius: 4px;
outline: none;
background-color: #41B883;
cursor: pointer;

... 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将使用`::before`伪类来设计 HTML 符号的样式。在我们检查输入字段后，下面的 CSS 将显示颜色和位置都很好的 HTML 符号。

```
...

content: '\2713';
display: block;
text-align: center;
color: #41B883;
position: absolute;
left: 0.7rem;
top: 0.2rem;

... 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！真的就这么简单。为了使复选框设计与页面的其余部分相匹配，不再需要 JavaScript 来实现这些样式。我们可以用这里提供的 CSS 安全地实现它。

下面是一段可以随意摆弄的代码:

[https://jsfiddle.net/proticm/2wzatymr//embedded/html,css,result//dark](https://jsfiddle.net/proticm/2wzatymr//embedded/html,css,result//dark)

## 进一步阅读

如果你对 CSS 变量感兴趣，看看我博客上发表的[这个](https://devinduct.com/blogpost/10/css-variables-cheat-sheet)或者[这个](https://devinduct.com/blogpost/11/ui-theming-with-css-variables)帖子。

参见[外观属性](https://developer.mozilla.org/en-US/docs/Web/CSS/appearance)的官方文件