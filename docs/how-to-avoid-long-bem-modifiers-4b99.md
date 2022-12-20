# 如何避免长边界元修饰语

> 原文：<https://dev.to/steffenpedersen/how-to-avoid-long-bem-modifiers-4b99>

在我们开始之前，我们应该达成共识。

# 什么是 BEM？🤔

首先，BEM 是一个命名约定。在我看来，它使 CSS 更容易阅读和理解。这使得它更容易扩展，通常也更容易使用。BEM 是整体概念的缩写。结构的要素是 **B** 锁、 **E** 元素和 **M** 修改器。这些例子的描述受到了官方指南的启发。

## 块

这是一个独立的实体，有自己的意义和目的。区块是`.button`。

```
.button {
    display: inline-block;
    padding: 1em;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 元素

这应该是块的一部分，没有独立的意义和用途。它应该在语义上绑定到它的块。元素是`&__text`。

```
.button {
    display: inline-block;
    padding: 1em;

    &__text {
        font-size: 1em;
        font-weight: 400;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 修改

这应该是块或元素上的标志。在这里，我们能够改变外表或行为。修改者是`&--bold`。

```
.button {
    display: inline-block;
    padding: 1em;

    &__text {
        font-size: 1em;
        font-weight: 400;

        &--bold {
            font-weight: 700;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最重要的规则之一是，你不能在一个元素里有一个元素，也不能在一个修饰语里有一个修饰语。

# 什么问题？🤔

使用 BEM 修饰符，命名约定会导致很长的选择器。这可能会损害可读性。

我们可以做这样的修饰语:

```
.button {
  &.--small {
    height: 2em;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样我们就可以这样做。

与其这样:

```
.button {
  &--small {
    height: 2em;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这导致更长的选择器`<button class="c-button c-button--small"></button>`。

当阅读输出的代码时，更容易得到一个概述。我们只是不需要在块外创建名为`--modifier`的类。那也很奇怪。

> 你怎么想呢?🤔什么能打破这个想法？🤔

感谢您的宝贵时间！

如果你喜欢这个，那么请❤️和 [*在 Twitter 上关注我*](https://twitter.com/mrsteffenp) 。