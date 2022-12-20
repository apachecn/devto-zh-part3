# 分解 JavaScript 析构语法

> 原文：<https://dev.to/irreverentmike/deconfusing-javascript-destructuring-syntax-22oj>

*(注:本文标题图片来自 [Unsplash](https://unsplash.com/search/photos/structure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [Dakota Roos](https://unsplash.com/photos/dSRhwPe6v9c?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 。谢谢你的工作！)*

在 es6(或 es2015 等)中，JavaScript 是一种非常有用的语言。它允许您从一个对象中提取值，并在一条语句中将它们赋给一个新变量。如果你以前没有遇到过析构，一个相当基本的例子是这样的:

```
const person = {
  name: 'Mike Bifulco',
  height: 556,
  fears: ['heights', 'fire'],
};
const { name } = person;
console.log(name); // this will print 'Mike Bifulco' 
```

Enter fullscreen mode Exit fullscreen mode

这里发生的事情很简单。我们正在创建一个名为`name`的`const`，并用在`person.name`中找到的值填充它。这是使用`=`左侧的支架结构完成的。

(如果你以前没见过或用过这个，那肯定会很混乱。不要为此责备你自己——这很快就会变成习惯了。)

通过析构，你可以做很多其他有用的事情。对我来说，其中最常见的是:

## 重命名

如果您想从一个对象中提取一个值，但是还需要对它进行重命名(也许是为了代码可读性)，您可以在一行中完成这个操作:

```
const person = {
  name: 'Mike Bifulco',
  height: 556,
  fears: ['heights', 'fire'],
};

const { height: currentHeight } = person;

console.log(currentHeight); // 556
console.log(height); // undefined! 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们在一行中把`person.height`赋给了`currentHeight`。太棒了。

## 提取

你可以从一个对象中提取一个值，然后用其他的东西创建另一个变量，你也可以这样做:

```
const person = {
  name: 'Mike Bifulco',
  height: 556,
  fears: ['heights', 'fire'],
};

const { fears, ...fearlessPerson } = person;

console.log(fearlessPerson.fears); // undefined!
console.log(fears[0]); // heights 
```

Enter fullscreen mode Exit fullscreen mode

这是析构语法和扩展操作符(`...`)的组合。非常得心应手！

## 硬模式:复杂解构

所以用析构来提取和重命名东西是相当简单的。我已经很舒服地使用这两个技巧有一段时间了。在过去的几个月里，我一直在和 [GatsbyJS](https://gatsbyjs.org) 探讨一些不同的项目(包括[我自己的网站](https://mike.biful.co))。当我开始定制和实现我自己的 Gatsby 主题时，我开始看到一些看起来非常复杂的析构语法，用于从整个站点的 GraphQL 查询中提取嵌套值。

我真是愣住了——乍一看，这不明显是怎么回事。让我们来看一个例子，这个例子来自一个 React 组件，用于呈现 Gatsby 主题中的单个页面:

```
const singlePage = ({
  data,
  location,
  pageContext: { nextPagePath, previousPagePath },
}) => {
  return (
    <p>...react component markup</p>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

在`singlePage` React 组件的定义中发生了一系列事情。让我们来看看这个析构，并简化一下:

```
const {
  data,
  location,
  pageContext: {
    nextPagePath,
    previousPagePath,
  },
} = props; 
```

Enter fullscreen mode Exit fullscreen mode

我们花了一些时间来观察，也花了一些时间来弄清楚这里完成了什么，所以让我们一步一步地来看一下:

1.  这个`const`的右边是`= props`，它告诉我们所有这些值都是从一个叫做`props`的对象中被析构的。
2.  前两个值`data`和`location`很有意义——它们看起来就像上面的例子。该行将从`props`对象中的这两个路径中的每一个创建一个`const`。
3.  事情变得有趣了——这是另一个解构。有点令人困惑的是，这里从道具中提取的值是`nextPagePath`和`previousPagePath`。没有为`pageContext`创建变量。

如果这个描述没有帮助-我不怪你。我真的花了一些时间去理解发生了什么。我甚至不确定我是否会推荐使用这种语法——它不是特别容易阅读，尤其是当像 prettier 这样的工具把它压缩成更少的行时。

也就是说，当从 Gatsby 上的 GraphQL 查询中提取数据时，这似乎是一条可行之路(可能在其他地方也是如此)。如果你要在那个世界工作，熟悉这里发生的事情是个好主意。

## 作业

我认为习惯这种语法的最好方法就是自己尝试一下。我创建了一个 JSBin，你可以用它来做一点实验。我很想听听你是怎么做的！点击查看[。](https://jsbin.com/kifizij/2/edit?js,console)

您面临的一些挑战:

*   将变量`displayName`重命名为`currentUserName`
*   将`editedBy`提取到它自己的变量中
*   创建一个名为`meta`的对象，其中包含除了`contactNumber`以外的所有方向

## 包装完毕

我很想听听你对此的看法——我发现自己写这篇文章是为了自助。很多时候，这些事情直到我独自练习一段时间后才会具体化。你对解构有什么创造性的用途？我做错了什么吗？大家说说吧。