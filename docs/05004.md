# Async Await Vs Promises，哪个更好？*恐慌*

> 原文：<https://dev.to/veebuv/async-await-vs-promises-which-one-is-better-panic-4a8f>

首先，它们是一样的😀

它们(异步等待)是使每个异步文件看起来漂亮的秘密。他们给任何人的眼睛带来喜悦，当他们离开地狱时看到同样的喜悦。

[![](img/b1ca46506fc2d3383977075f3936a0b0.png)](https://i.giphy.com/media/105oA1tkTBSy9a/giphy.gif)

你猜对了，这是另一个关于异步等待 vs 承诺的博客。但是别担心，我会写得很短，希望这是你需要阅读的最后一篇。

如果您因为对发生了什么或者 Async Await 如何如此神奇地工作感到有点困惑而感到困惑，这个抽象的解释可以提供一点帮助。

首先让我们创建一个承诺:

```
const getData = new Promise((resolve, reject) => {
  // crazy calculation that takes 10 seconds and spits out the      //number 55
  const data = 55;
  if (data) {
     resolve (55);
  } else {
     reject ("Error state");
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

假设我们想要调用这个函数——我们与它交互的方式是使用 resolve 回调输出。

也许有一个按钮，当点击它时会调用一个`fetchInfo`函数——如果我们使用`then`属性解析我们的代码，我们最终会到达:

```
const fetchInfo = getData().then((data) => {
    return fetchFromDB(id).then((dbData) => {
      return `This is the dbData: ${dbData}`
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在看起来还不算太糟……但是用 Async Await 看起来会更好——自己看看吧。异步等待等同于上述

```
const fetchInfo = async () => {
   const id = await getData();
   const dbData = await fetchFromDB(id);
   return `This is the dbData: ${dbData}`
} 
```

Enter fullscreen mode Exit fullscreen mode

我想就是这样——简单明了的对比。

Async-Await 的核心是将代码可读性线性化，因为有时迷失在`thens`中会使跟踪代码路径变得困难。

通过在 async 中包装一个函数，JS 将它读取为一个承诺，函数中的 await 充当 then 的`equivalent`来解析`getData`承诺

* * *

在 LinkedIn || [Twitter](https://twitter.com/veebuv) 上关注我，会有更多文章

如果你有一个你想让我写的话题，发微博给我😀

此外，我总是喜欢在评论中得到反馈👇如果这对你有帮助，点击❤️来支持这篇文章