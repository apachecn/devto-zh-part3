# 对打字稿中简洁承诺处理的一点看法。

> 原文：<https://dev.to/kcvgan/my-take-on-concise-promise-handling-in-typescript-g2h>

在寻找一种干净的方法来处理 async/await REST 调用而不使用 try-catch 块之后，我找到了一个满足我需要的小包装器。

启发我的文章是[https://blog . Grossman . io/how-to-write-async-await-without-try-catch-blocks-in-JavaScript/](https://blog.grossman.io/how-to-write-async-await-without-try-catch-blocks-in-javascript/)。

```
function on (promise) {
    return promise
      .then(response => [response, null)
      .catch(error => [null, error])
}

/// async function
async function doSomething() {
    const [data, error] = await on(somePromise())
    if (data) {
        // do whatever
    } else {
        // handle error
    }
} 
```

在我开始做这样的事情后，上面的解决方案就像是一个灵光乍现的时刻

```
promise
    .then(result => doSomething(result))
    .catch(error => log(error)) 
```

还有这个:

```
 try {
        const data = await somePromise()
        // do whatever
    } catch (e) {
        // handle error
    } 
```

在使用 React 和学习挂钩在前端做了大量工作后，本文中概述的解决方案的简单性就被点击了。然而，在代码审查期间，一位同事指出，虽然很好，但它根本没有输入。然后他建议正确地输入。

就是这里:

```
// using Axios but you can type your promises however you'd like
const on = <RES, ERR = any> (promise: AxiosPromise): Promise<[RES | null, ERR | null]> => {
  return promise
    // destructuring since my API returns all the good stuff like so.
    .then(({ data }): [RES, ERR | null] => [data, null])
    .catch((error): [RES | null, ERR] => [null, error])
}

// and now calling it from an async function
const [todo, error] = await on<Todo>(somePromise())
// now my returned todo is already typed as Todo.
// error typing is optional, it defaults to any. 
```

我发布我的解决方案只是为了分享我的观点。我非常乐意接受改进和评论。你会使用这样的解决方案吗？让我知道。这也是我的第一篇文章/帖子。因为我一直想写我做的事情，所以我决定这样一个简短的主题可能足以让我尝试一下。

干杯！