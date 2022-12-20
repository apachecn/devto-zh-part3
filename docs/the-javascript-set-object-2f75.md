# JavaScript Set 对象

> 原文：<https://dev.to/keevcodes/the-javascript-set-object-2f75>

前几天在做一些工作时，我遇到了一点问题，并了解到一些我认为非常有用的东西，所以我想“嗯，管他呢，为什么不就此写一个帖子呢？

我的团队整合了一个**所见即所得**(顺便说一句...这显然代表着所见即所得，我在这个行业工作了三年，现在才知道)
进入我们的 React 应用程序的后端来创建帖子。这些帖子有两列，可以返回*降价*、*按钮*或*资产*(图片和视频)。这些数据从我们的 graph QL API 返回，作为两个条目的数组，类似于这样

`Array [Object{type: "text"}, Object{type: "asset"}]`

现在，这两个数据位将被包装到一个两列布局的组件中，并根据其类型(例如:*文本*、*资产*或*按钮*)呈现一个不同的组件。

## 如此...有什么问题？

在我们的 **WYSIWIG** 编辑器中，任何写在我们第一列中的超出最大单词长度的文本，都会换行到我们的第二列，将我们的数据返回给一个`{type: "text"}`字段。如果你熟悉 React，为了返回 **markdown** ，我们在元素上使用`dangerouslySetInnerHTML` prop 并传入我们的 markdown 数据。

因此，如果我们使用`Array.filter()`方法来呈现适当的`<Image />`、`<Text />`或`<Button />`组件，如果`Array`包含两个文本类型的对象，我将两次呈现 markdown...显然不可取。

## 所以就像，从你的数组中删除重复的哥们？

[![](img/3e58c19f27bdc57a4b5c7f92e6e712bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VUEJcwSC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zepop3x2pvb0iipj24i1.jpg)

所以我需要从我的 posts 数组中删除任何重复的内容，这很容易做到。我们可以首先在我们的 posts 数组上使用`Array.Map()`来获取每个帖子的类型，然后使用`Array.filter()`方法来比较前一个条目和当前条目，给我们一个新的数组，然后我们可以对原始数组进行测试，以确保我们得到了预期的结果。

`let postTypes = posts.map({post} => post);
let filteredPosts = postTypes.filter((type, i) => types.indexOf(type) === i));`

这段代码没有任何问题。推理起来相当简单，我们没有做任何数据突变，我们把副作用从我们的地图中排除出去。然而，在 JavaScript 中有一种更简单的方法来返回唯一的对象列表(实际上是任何原语)...`Set`对象。

## 设定的对象是什么？

`Set`是一个可以存储任何类型的 JavaScript 原语的对象(这里想一下*布尔值*、*字符串*、*数字*)，但是它只会存储**唯一的**原语。 ***等等...什么？？？*** 太牛了！**耶**，我的开发者伙伴，**耶**。

我们所要做的不是使用 filter 方法，而是将我们的`postTypes`数组传递给`Set`对象，并将其包装成一个数组。

`let filteredPosts = [... new Set(postTypes)];`

还有 ***嘭！*** 删除重复项后的全新数组。但是，这不是所有的乡亲！`Set`对象还附带了一些非常方便的方法，比如`add()`和`includes()`来添加或查找你的集合中的对象。

我建议看一看 [Mozilla Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) 文档，里面有你可以使用的所有很酷的方法。