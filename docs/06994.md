# 用承诺解决你所有的问题

> 原文：<https://dev.to/irreverentmike/solve-all-your-problems-with-promise-allsettled-444i>

(注:这篇文章的灵感来自于[韦斯·博斯](https://twitter.com/wesbos)在 JAMstack_conf_nyc 的一次演讲。谢谢你的提示，韦斯！)

最近，我发现自己在构建越来越复杂的 JavaScript web 应用程序。如果你熟悉现代 JavaScript，你肯定遇到过[`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)——一种帮助你异步执行代码的结构。一个`Promise`就像它听起来的那样:你用它们来执行代码，这些代码将(承诺)在未来的某个时刻返回一个值:

看看这个有点做作的例子，在这个例子中，我们异步加载博客帖子上的评论:

```
const loadComments = new Promise((resolve, reject) => {
  // run an asynchronous API call
  BlogEngine.loadCommentsForPost({ id: '12345' })
    .then(comments => {
      // Everything worked! Return this promise with the comments we got back.
      resolve(comments)
    })
    .error(err => {
      // something went wrong - send the error back
      reject(new Error(err))
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

还有一种替代的语法模式，`async` / `await`，让你以一种更清晰的伪序列形式写下承诺:

```
const loadComments = async () => {
  try {
    const comments = await BlogEngine.loadCommentsForPost({ id: '12345' })
    return comments
  } catch (err) {
    return new Error(err)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 应对多重承诺

不可避免地，你会发现自己处于需要履行多个承诺的情况。让我们简单地开始:

```
 const postIds = ['1', '2', '3', '4', '5'];
postIds.forEach(async (id) => {
  // load the comments for this post
  const comments = await loadComments(id);

  // then do something with them, like spit them out to the console, for example
  console.log(`Returned ${comments.length} comments, bru`);
}) 
```

Enter fullscreen mode Exit fullscreen mode

轻松点。快速循环可以让我们对感兴趣的每篇文章都有评论。但是这里有一个陷阱——`await`关键字将停止循环的执行，直到`loadComments`为每个帖子返回。这意味着我们按顺序加载每个帖子的评论*，而不是利用浏览器一次发送多个 API 请求的能力。*

 *一次发送多个请求的最简单方法是使用`Promise.all()`。这是一个函数，它接受一个由`Promise` s 组成的*数组，并返回一个包含每个承诺的响应的数组:* 

```
const postIds = ['1', '2', '3', '4', '5'];
const promises = postIds.map(async (id) => {
  return await loadComments(id);
};

const postComments = Promise.all(promises);

// postComments will be an Array of results fromj the promises we created:
console.log(JSON.postComments);
/*
[
  { post1Comments },
  { post2Comments },
  etc...
]
*/ 
```

Enter fullscreen mode Exit fullscreen mode

还有**一个重要的抓(lol)** 跟`Promise.all()`。如果*发送给`Promise.all()`的任何*承诺失败或者`reject`失败，*一切*失败。来自 [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)(重点是我的):

> 当作为 iterable 传递的所有承诺都已解析或 iterable 不包含承诺时，`Promise.all()`方法返回一个解析的`Promise`。**它以第一个拒绝的承诺的理由拒绝。**

该死的，事实证明`Promise.all()`在执行策略上相当保守。如果你没有意识到这一点，这可能是非常危险的。在上面的例子中，如果为*一篇文章*加载评论导致*每篇文章*的评论都无法加载，这不是很好吗？该死的。

## *输入`Promise.allSettled()`*

直到最近，对于这种情况还没有一个令人惊叹的答案。*然而*，我们将很快广泛使用 [`Promise.allSettled()`](https://github.com/tc39/proposal-promise-allSettled) ，这是目前 ECMAscript 技术委员会 39 面前的第三阶段提案，该委员会负责批准和认可 ECMAscript(也称为“JavaScript”，联合国发起的)的变更。

你看，`Promise.allSettled()`做的正是我们在上面的例子中想要的加载博客评论。如果*传递给它的任何一个*失败，它不会失败，而是等待直到它们都完成执行(换句话说，直到它们都“解决”)，并从每个返回一个数组:

(这个代码样本抄袭自 github 提案——去看看更多细节)

```
const promises = [fetch('index.html'), fetch('https://does-not-exist/')]
const results = await Promise.allSettled(promises)
const successfulPromises = results.filter(p => p.status === 'fulfilled') 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！超级好用。

## 现在使用`Promise.All()`(更新！)

**2019 年 4 月 26 日更新**
安装 [`core-js`](https://github.com/zloirock/core-js) 包，并将其包含在您的代码库中:

```
import 'core-js/proposals/promise-all-settled' 
```

Enter fullscreen mode Exit fullscreen mode

**原帖:**
好了，事情是这样的——这就是棘手的部分。我写这篇文章时认为，这就像告诉你在项目的`.babelrc`配置中使用一个`stage-3`预置一样简单。事实证明，截止到 v7，Babel 已经[停止发布舞台预置](https://babeljs.io/blog/2018/07/27/removing-babels-stage-presets)！如果这对你有任何意义，你应该看看他们的帖子。

现在的答案是，*不是*使用`Promise.allSettled()`还不是一个好主意，因为它没有得到广泛的支持。首先，据我所知，没有一个 babel config 扩展可以为你的项目增加支持。目前，你能得到的最好的是一个 polyfill 或者一个实现了`allSettled()`的可选库。

我知道这可能会令人失望——可以肯定的是，我遇到的十几个问题都可以用这个新语法很好地解决。不过，我想让你们关注的是，JavaScript 的持续增长是多么惊人。看到这些对语言的补充也在公开的中进行[是令人兴奋的，也是非常酷的。开源是如此美好的事情！](https://github.com/tc39/proposal-promise-allSettled)

如果你真的想在你的代码中使用`Promise.All()`,你最好以某种方式为这个过程做出贡献。这可能小到编写自己的 polyfill，或者向 tc39 相关人员提供反馈，或者使用其他库。

### 脚注

我会尽我所能保持这篇文章的更新。当`allSettled`发布时，我会让你们知道。👍

*(本文封面照片由[瓦伦丁·安托努奇](https://unsplash.com/photos/9cRDDvhpBRw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/promise?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。谢谢你的工作！)**