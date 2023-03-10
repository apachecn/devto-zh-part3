# 我写了一个 NPM 包来防止对象中的无限递归

> 原文：<https://dev.to/ricardo/i-wrote-an-npm-package-to-prevent-infinite-recursion-in-objects-5422>

有时我们需要从一些对象输出数据，但是根据对象的结构，这个简单的任务可能很难完成，让我们创建两个小对象来演示这一点:

```
const a = { hello: 'world' };

const b = { hey: 'hey', hello: a };

a.newProp = b; 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们有两个对象，a 和 b，最后我们使用 newProp 属性连接这些对象，简单吧？

但是当我们试图输出一个对象内容，比方说，到一个 JSON 字符串时，会发生什么呢？

```
JSON.stringfy(a)
Uncaught TypeError: Converting circular structure 
to JSON at JSON.stringify (<anonymous>) at <anonymous>:1:6 
```

Enter fullscreen mode Exit fullscreen mode

问题是我们在 a 和 b 之间创建了一个连接，但是 b 之前是用 hello 属性中的 a 连接的，这使得一旦这个连接是无限的，就不可能生成 JSON 结构。

[![](img/9e9841936de850246603350041e79b61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M6AL46R_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2780/1%2A_k0QKqdbEEfOOJjRRvsFnw.png)

为了解决这个问题，我们需要创建一个函数来生成一个新的对象，而不需要无休止的递归，但是我们也需要像这样保持有限的递归:

```
const enzo = { 
  name: 'Enzo', 
  otherSon: null, 
}

const validStructure = {
  name: 'John',
  son: enzo,
  otherSon: {
    name: 'Ridlav',
  },
}

enzo.otherSon = validStructure.otherSon

JSON.stringfy(validStructure) 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们有以下结构:

*   约翰有两个儿子，一个**恩佐**和另一个**里德拉夫**。

*   恩佐有一个儿子，名叫**里德拉夫**。

该结构的直观表示将是:

[![](img/ad836eda5f9cba00f1a3e17db757c4c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EdEt6ZjF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Ach7t7IIpNKqSWUieSfJUIQ.png)

这个(奇怪的)结构是一个有效递归的好例子，我们的函数应该在最终对象中保留它，为了解决这个问题，我们需要使用递归来删除递归！

让我们开始声明我们的函数及其参数:

```
function preventObjectRecursion(root, list = []) {

} 
```

Enter fullscreen mode Exit fullscreen mode

让我们理解这个函数的参数:

*   **Root:** 将接收我们需要改变的对象，但是在第二次调用中还将接收原始对象的属性。

*   **List:** 将接收路径中的对象从根到当前的属性，这将帮助我们检查这个属性以前是否被添加过

想法是对每个对象属性调用这个函数，检查这个属性以前是否在这个相同的路径中被调用过，但是首先我们需要忽略具有其他数据类型(null、function、boolean、strings 和 numbers)的根，一旦这些类型不生成递归:

```
if(!root || typeof root !== 'object') return root 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们需要将当前根与列表数组中的对象进行比较，这里的想法是避免这种情况:

[![](img/159c0bc62269b66b0ac7ae539c56f9da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nw5ZK6Zc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3956/1%2A6c2ivDUE0sWMvrKv0_775w.png)

在上面的例子中，我们在根中有一个对 a 的引用，在 b 属性中有另一个对 a 的引用，为了解决 list 参数将存储路径中的所有元素(在这个例子中是 a，b)并检查这个元素是否是其中之一，如果是，我们避免这个元素被添加到我们的新数组中:

```
if(list.length > 0){
  const hasDuplicated = list.some((item: object) => item === root);
  if(hasDuplicated) return {}
} 
```

Enter fullscreen mode Exit fullscreen mode

好的，检查之后，我们需要在列表路径中添加当前的根，这将允许我们在下次调用函数时分析这个元素:

```
list.push(root) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要分析当前根元素的属性，并再次调用这个函数来消除任何递归，我们将 Object.keys 来完成:

```
return Object.keys(root)
  .reduce((rootClean, key) => {
    rootClean[key] = preventObjectRecursion(root[key], [...list])
      return rootClean
  }, {}) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想了解更多我写这篇文章的 reduce 方法:
[#2 函数式方法:Reduce…medium.com](https://medium.com/@_rxluz/2-functional-approach-reduce-c882c68c4cdf)

与我发布的包和上面的例子唯一不同的是，我使用了 typescript 而不是 Javascript 来创建这个包，你可以在这个 Github repo 中查看结果:
[rx Luz/prevent-object-recursion…github.com](https://github.com/rxluz/prevent-object-recursion)

或者直接从 NPM 安装:

```
$ npm i @rxluz/prevent-object-recursion --save 
```

Enter fullscreen mode Exit fullscreen mode

此外，请随时向我发送评论反馈或在 Github 中打开[一个问题](https://github.com/rxluz/prevent-object-recursion/issues)，这个回购需要增加测试的数量，因此欢迎贡献者！

这就是所有的人！
[![](img/46e3245cd60ea9b4e05ba66c91634d92.png)T3】](https://i.giphy.com/media/xUPOqo6E1XvWXwlCyQ/giphy.gif)