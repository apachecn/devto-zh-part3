# 少即是多

> 原文：<https://dev.to/emgodev/less-really-is-more-1250>

前几天，我开始使用 LESS，诚然，我不知道这不是萨斯/SCSS 的组成部分。我现在喜欢上它了。如果你用过 SASS，也许你知道为什么？或者也许我不知道如何使用 SASS？

# 关于萨斯的几件事

例如，SASS 大量使用关键字和奇怪的符号，这与前端语法不太匹配。

变量以' $ '为前缀，除此之外它类似于 LESS 和 JS。

```
$text-color: #06f; 
```

Enter fullscreen mode Exit fullscreen mode

如果你想共享另一个规则集(类、元素、id)的声明，你可以使用“extend”关键字或者一个类似函数的 mixinMixins 使用“include”关键字。

```
@mixin easyFlexContainer( $type, $flow, $justify, $items, $content ){
  display: $type;
  flex-flow: $flow;
  justify-content: $justify;
  align-items: $items;
  align-content: $content;
}

.ulIsAnnoyingNavElement {
  padding: 0em;
  margin: 0em;
}

.main-nav {
  ul {
    extend .ulIsAnnoyingNavElement;

    include easyFlexContainer(flex, row wrap, flex-start, center, flex-start);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

不要误解我，这一切都很好，你可以习惯它，但一个简单的比较将有望解释为什么我认为越少越好。

# 怎么就没那么牛逼了(在我看来)？

只需查看 SASS 的几个不同组件，您就可以大致了解会发生什么。这就是少与多的区别。less 把很多前缀归结为两件事，LESS 前缀用' @ '，LESS 构造是函数。

我说的 less 'constructs '是' functions '的意思是，你可以把 less 中的东西想象成字面上可用的函数，不需要神奇的关键字，直接用就行了！

```
@text-color: pink;

.makeItPretty {
  padding: 0em;
  margin: 0em;
}

.easyFlexContainer( @type, @flow, @justify, @items, @content ){
  display: @type;
  flex-flow: @flow;
  justify-content: @justify;
  align-items: @items;
  align-content: @content;
}

/* Client Ruleset */
.main-nav
  ul {
    .makeItPretty();
    .easyFlexContainer(flex, row wrap, flex-start, center, flex-start);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，这就是我想分享的。LESS 相当神奇，把类当成函数和 mixins，或者不使用'()'的时候，就当类！