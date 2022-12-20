# 如何对 Array.map 使用 Async/Await？

> 原文：<https://dev.to/jhalvorson/how-do-i-use-async-await-with-array-map-1h3f>

[![Await](img/5f9306bc30fba3c7321411f1140d092c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3ctmbE8m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e6dpui3dg7zcxxobdr3x.jpg)

不能，但是可以做类似的事情。这总是让我出错，所以我想我最好把它写下来。

你不能异步/等待`Array.map`，因为**同步代码不会等待你的异步代码解决**，相反它会触发函数并继续前进。这是我们想要的行为，因为我们不希望我们的`Array.map`阻塞线程，否则当`Array.map`去做它的事情时什么都不会运行。知道这很酷，但是，如果我们需要等待我们的`Array.map()`，它并不能真正帮助我们。

谢天谢地，有一个解决方案，我们可以通过利用`Promise.all`来实现我们想要的

例如，以下内容不起作用:

```
const postIds = ['123', 'dn28e29', 'dn22je3'];

const posts = await postIds.map(id => {
  return axios
    .get(`https://example.com/post/${id}`)
    .then(res => res.data)
    .catch(e => console.error(e));
}

console.log(posts) // [] it returns the promise, not the results 💩 
```

Enter fullscreen mode Exit fullscreen mode

但是这将:

```
const postIds = ['123', 'dn28e29', 'dn22je3'];

const posts = posts.map(post => {
  return axios
    .get(`https://example.com/post/${post.id}`)
    .then(res => res.data)
    .catch(e => console.error(e));
}

Promise.all(posts).then(res => console.log(`We have posts: ${res}!`)); 
```

Enter fullscreen mode Exit fullscreen mode

我们没有立即返回`Array.map`，而是将它赋给一个变量，并将该变量传递给`Promise.all`，一旦解析完毕，我们就可以访问由`Array.map`返回的新数组。

祝 Java 编写愉快。