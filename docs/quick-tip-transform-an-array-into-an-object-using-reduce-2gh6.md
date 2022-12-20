# 快速提示:使用。减少()

> 原文：<https://dev.to/_bigblind/quick-tip-transform-an-array-into-an-object-using-reduce-2gh6>

我不想让你花时间去读一些冗长乏味的介绍，下面是这篇文章的要点:

假设你有一个这样的数组:

```
[
    {id: 1, category: "frontend", title: "All About That Sass"},
    {id: 2, category: "backend", title: "Beam me up, Scotty: Apache Beam tips"},
    {id: 3, category: "frontend", title: "Sanitizing HTML: Going antibactirial on XSS attacks"}
] 
```

你想得到一个以类别作为关键字的对象，映射到具有该类别的文章 id，就像这样:

```
{
    frontend: [1, 3],
    backend: [2]
} 
```

你可以用我们的朋友 [`Array.prototype.reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 来说明这个。

```
const posts = [
    {id: 1, category: "frontend", title: "All About That Sass"},
    {id: 2, category: "backend", title: "Beam me up, Scotty: Apache Beam tips"},
    {id: 3, category: "frontend", title: "Sanitizing HTML: Going antibactirial on XSS attacks"}
];

const categoryPosts = posts.reduce((acc, post) => {
    let {id, category} = post;
    return {...acc, [category]: [...(acc[category] || []), id]};
}, {}); 
```

好吧，让我们看看这是怎么回事。

我认为`reduce`好像把我的数组变成了**管道**。这条管道获取一些初始值，将数组中的每个值作为一个单独的步骤应用，并返回新值。这个从一个步骤传递到另一个步骤的值通常被称为**累加器**，因为它在通过管道时累加变化。累加器的初始值作为第二个参数传递给`reduce`。在这种情况下，它是一个空对象。那么数组的元素是如何应用到累加器上的呢？这取决于你给第一个参数`reduce`的函数。无论从那个函数返回什么，都被用作累加器的新值。

```
(acc, post) => {
    let {id, category} = post;
    return {...acc, [category]: [...(acc[category] || [])], id]};
} 
```

这个函数将累加器作为第一个参数，将数组中的一个元素作为第二个参数。第一行使用[对象析构](https://dev.to/_elmahdim/destructuring-in-javascript-87n)将文章的类别和 id 提取到它们自己的变量中。这只是给我们一个漂亮的短变量名，让下一行更整洁一些。

```
return {...acc, [category]: [...(acc[category] || [])], id]}; 
```

我在这里使用了很多 ES6 语法，可能不是每个人都熟悉 win，所以让我们深入研究一下。

```
return {...acc} 
```

如果我们只是返回这个，我们只是返回累加器的初始值，因为它前面的这个`...`叫做[展开](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#Spread_in_object_literals)。在 object literal 中，它获取给定对象的所有属性和值，并将它们放入新创建的对象中。上面这行代码所做的，就是获取累加器的属性，并把它们放入我们返回的对象中。

```
return {...acc, [category]: [...(acc[category] || [])], id]}; 
```

接下来你可能会注意到这个`[category]:`语法。这是一个[计算属性名](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)。其思想是，您可以在对象文字中定义一个属性，而无需事先知道属性名。在上面的一行中，属性名就是类别。

我们希望这个属性最终包含一个数组，该数组包含了这个类别中所有文章的 id，所以让我们来看看我们赋予这个属性的值:

```
[...(acc[category] || [])], id]} 
```

这里我们又有了扩展语法，但这次是在数组文字中。与 object spread 语法类似，它从提供给它的数组中获取所有值，就好像它们是写在这个数组文本中一样，将它们插入到新创建的数组中的那个位置。

这给了我们一个非常简洁的方法来定义一个数组，这个数组就是附加了一个或多个值的其他数组。

```
const a = [1, 2, 3];
const b = [...a, 4]; // b = [1, 2, 3, 4] 
```

因此，在我们的 posts 示例中，我们希望将帖子的 id 附加到累加器已经拥有的 id 中，所以我们只需编写:

```
[...acc[category], id]} 
```

但是如果我们的累加器还没有该类别的任何帖子呢？(这在所有类别的开始都是正确的)那么`acc[category]`就是`undefined`，spread 语法只对数组这样的可迭代值有效，所以我们得到了一个`TypeError`。

```
[...(acc[category] || [])], id]} 
```

因此，我们取表达式`acc[category] || []`(用大括号括起来，所以 spread 语法适用于所有内容。在第一个值是 falsy 的情况下,`||`操作符返回第二个值(T2 是 falsy ),所以如果我们的累加器没有任何给定类别的文章，我们将只扩展空数组，导致在我们的新 id 之前没有值被添加。

所以，让我们把这些放在一起:

```
const posts = [
    {id: 1, category: "frontend", title: "All About That Sass"},
    {id: 2, category: "backend", title: "Beam me up, Scotty: Apache Beam tips"},
    {id: 3, category: "frontend", title: "Sanitizing HTML: Going antibactirial on XSS attacks"}
];

const categoryPosts = posts.reduce((acc, post) => {
    let {id, category} = post;
    return {...acc, [category]: [...(acc[category] || []), id]};
}, {}); 
```

在 posts 数组上调用`reduce`,用一个空对象作为初始累加器，对于每个 post 我们:

*   提取文章 id 和类别
*   获取累加器的所有现有属性，并应用返回值。
*   如果累加器已经有一个文章类别的 id 数组，我们将文章的 id 追加到这个数组中。否则，我们为该类别创建一个新的空数组，并添加我们帖子的 id。
*   我们从传递给`redused`的函数返回的值被用作数组中下一个 post 的累加器，并在所有 post 都被处理后从`reduce`返回。

# 反馈💬

这是我的第一个有点面向初学者的帖子，我希望你有任何建设性的反馈！。我觉得我用了太多的 ES6 语法使这篇文章过于复杂，然后觉得有必要解释一下。我想我应该把它保持在最低限度，并且坚持使用`reduce`的核心概念。我可能还会写一个更集中的版本，但是现在，这是我所拥有的。

对于很好理解函数式编程的人来说，这种使用`reduce`的方式可能也是非常明显的。但是我编程生涯的大部分时间都花在了编写过程化和面向对象的代码上。我很快就对地图有了直观的感觉，但在我使用`reduce`的所有方式上，我仍然有一些小灵感。