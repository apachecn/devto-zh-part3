# 将布局与内容分开

> 原文：<https://dev.to/conw_y/separate-layout-from-content-5h14>

只是一个简短的帖子，分享一个我已经应用了一段时间的简单的样式实践:总是将布局样式与其他样式分开。

假设我想呈现一个布局，其中有一个大项目和两个小项目:

```
 +-------------------------------------------------------+
  | #item-name            | #edit-button | #delete-button |
  +-------------------------------------------------------+ 
```

## 坏道

我可以这样设计元素:

```
#container { display: flex; }
#item-name { flex: 1; /* ...other styles */ }
#edit-button { flex: .2; /* ...other styles */ }
#delete-button { flex: .2; /* ...other styles */ } 
```

注意在上面的代码片段中，布局样式是如何在容器和孩子之间展开的。这至少有两个实际的可维护性问题:

*   如果我决定将一个子元素换成另一个子元素，那么我必须记住用与前一个相同的`flex`来设计新元素的样式，以便保持布局。例如，如果我删除了`#item-name`及其样式，并在它的位置创建了一个新元素，例如`#item-image`，那么我必须记住将`flex: 1`应用到`#item-image`。

*   如果我想改变布局，我可能需要修改代码不同部分的许多不同元素。例如，如果我想改变布局，使所有的元素具有相同的宽度，那么我可能不得不搜索许多行代码，以便找到并改变选择符`#item-name`、`#edit-button`和`#delete-button`的`flex`属性。

基本问题是，与布局相关的代码与与单个元素相关的代码混杂在一起。这段代码的关注点分离很差。每个“模块”或样式声明迫使我一次考虑多个关注点(即布局和特定于组件的关注点)，因此很难在不影响多个模块的情况下做出改变。这是违反[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)的一个例子，它告诉我们，我们应该设计我们的模块，使它们彼此隐藏“可能改变的设计决策”。

## 更好的方法

一个更好的方法是**将所有布局关注点放在一起**，与其他关注点分开。

这可以通过使用 CSS [直接派生](https://www.w3schools.com/Css/css_combinators.asp)子选择器来选择直接子节点，以及使用 [`:first-child`](https://www.w3schools.com/cssref/sel_firstchild.asp) 伪选择器来填充第一个子节点，而其余的子节点占 20%。

```
/* layout styles on container ⬇️ */

#container {
  display: flex;

  > * {
    flex: .2;

    &:first-child {
      flex: 1;
    }
  }
}

/* non-layout styles on individual elements ⬇️ */

#item-name {
  /* item-name styles */
}

#edit-button {
  /* edit-button styles */
}

#delete-button {
  /* delete-button styles */
} 
```

(注意:上面的代码片段假设支持嵌套样式，使用预处理器，如 [Less](http://lesscss.org/) 或 [SASS](https://sass-lang.com) 。)

现在，我可以重新排序、交换和更改我的子组件，而不必记住设置它们各自的宽度。布局组件为我处理了所有这些。我可以查看每个组件的样式块，只关注该组件的样式，而不用考虑布局。

同样，我可以改变布局的外观，而不必检查单个组件的样式。我可以将方向改为垂直，或者将一个孩子变大，甚至将整个布局改为使用 [CSS 网格布局](https://www.w3schools.com/css/css_grid.asp)，所有这些都不需要修改单个孩子的样式。

用[鲍伯·马丁](https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html)的话说，我们想要“把因为同样原因而改变的事物聚集在一起”...把那些因不同原因而变化的东西分开”。