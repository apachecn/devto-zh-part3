# SCSS:与号规则

> 原文：<https://dev.to/devdiaries/scss-ampersand-rules-4jg0>

## 解释

在声明后使用`&`,你可以设置一个 css 规则来应用，因为有&的
选择器实际上在它是嵌套元素的父元素时应用。🤔？所以基本上，你从里到外阅读规则。所以在第一个例子中你读到如果`.parent-in-this-case` &是`.some-class`的父，那么应用这个规则。

## 为什么？🧐

那么你为什么要这么做呢？一个例子是仅影响 ie-9 的样式，例如，如果您在浏览器是 ie-9 或更低版本时应用一个类，您可以在一个块内设置特定的样式，该样式仅在 ie-9 是 html 标签上的父类时应用。在这里查看文件[。scss 中的`&`提供了大量的功能，但也可能有点混乱，所以要注意你的用法！让我们来看看一些例子:](https://sass-lang.com/documentation/file.SASS_REFERENCE.html#parent-script) 

```
# scss
.some-class {
    font-weight: normal;

    .parent-in-this-case & {
        font-style: italic;  
  }
} 
```

编译成这个 css:

```
.some-class {
  font-weight: normal;
}
.parent-in-this-case .some-class {
  font-style: italic;
} 
```

以下是一些其他有用的与号用法:

```
.el {
    font-size: 12px;
    &:hover {
        color: blue;
    }
    &.active {
        font-weight: bold;
    }
    &--larger {
        font-size: 14px;
    }
} 
```

编译成这个 css:

```
.el {
  font-size: 12px;
}
.el:hover {
  color: blue;
}
.el.active {
  font-weight: bold;
}
.el--larger {
  font-size: 14px;
} 
```

这里您可以看到,“与”符号的便利和强大。如果你使用的是类似于 [BEM](http://getbem.com/introduction/) 的东西，那么添加自定义元素和修饰符类会变得非常容易和快速。

[开发人员日记上的原始帖子](https://www.dev-diaries.com/social-posts/scss-ampersand-rule/)
[开发人员日记上的 Instagram 帖子](https://www.instagram.com/p/BvGkv6DgDr4)