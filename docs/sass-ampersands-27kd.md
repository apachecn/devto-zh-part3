# 萨斯&符号

> 原文：<https://dev.to/jibrankalia/sass-ampersands-27kd>

我喜欢在 Scalefactor 工作的众多原因之一是，我可以在前端和后端广泛工作。目前来说，后端是我的强项。这就是为什么你可能会在这里看到更多的前端相关职位，因为我试图在这方面的水平。回到萨斯。这是我今天学到的一件小事。借用下面 [CSS-Tricks](https://css-tricks.com/the-sass-ampersand/) 的解释。

下面是一个在 SASS 中工作的简单嵌套示例:

```
.parent {
  .child {}
} 
```

在 css 中编译成:

```
.parent .child {} 
```

当我们使用一个`&`
的时候呢

```
.first-parent {
  &.second-parent {}
} 
```

在 css 中编译成:

```
.first-parent.second-parent {} 
```

两者的区别在于，在第二个例子中，我们选择了一个既有类又有`first-parent`和`second parent`类的**元素。**

我喜欢把`&`想象成复制父选择器并粘贴到子选择器旁边。当与 [BEM CSS 方法论——块元素修改器](http://getbem.com/introduction/)一起使用时，这变得很有趣。稍后会详细介绍。

`&`在 sass 中有更多的用法。《CSS-Tricks》中的这篇文章解释了一切，令人惊叹。

感谢您的阅读！