# 讨厌的捡球

> 原文：<https://dev.to/mzubairahmed/the-abominable-fetch-1b2h>

我得到的第一个问题是为什么使用 Fetch API 而不是 Axios 库。网上有很多帖子很好地展示了两个图书馆的相似和不同之处。对我们来说，有效的方法将在其他时间讨论。

让我们跳到柱子上。

### 当前形势

我们的 fetch 调用写起来又长又麻烦。

```
await fetch(URL_GOES_HERE, {
   method: 'GET',
   mode: 'cors',
   credentials: 'same-origin',
   headers: {
     Authorization: `token ${this.props.user.loggedInUser.token}`,
     'Accept-Language': language,
     'Content-Type'] = 'application/json'
   }
 }
); 
```

从 API 中“获取”大量代码。上面的大部分锅炉板代码在整个应用程序中重复用于其他类似的获取功能。很明显，模式、凭证、头可以被抽象出来，并与其他获取函数共享。

### 改进

为了能够有效地做到这一点，创建了一个 createFetch 包装器，它将默认值传递给 Fetch。这个新的包装器获取然后通过 Context React API 传递给 app。

```
context.fetch = createFetch(window.fetch, {
    baseUrl: context.apiConfig.apiUrl,
    language: store.getState().intl.locale,
    authToken: store.getState().user.loggedInUser.token 
```

你可能想知道，甚至在应用程序启动之前，令牌是从哪里来的。因此，这个上下文是在启动应用程序之前调用适当的 API 之后创建的。

createFetch 包装器由默认值组成，这些默认值将添加到 Fetch 函数的选项中。如下所述:

#### 标题

```
let options = {};

// language variable is passed down from createFetch params. 
options.headers[’Accept-Language’] = language;

// Since mostly JSON object is returned. 
options.headers[’Content-Type’] = 'application/json’;

// authToken variable is passed from createFetch params. 
options.headers.Authorization = `token ${authToken}`; 
```

#### 模式

```
options.mode = 'cors'; 
```

#### 凭证

```
options.credentials = 'same-origin'; 
```

只有在没有显式传递的情况下，才会分配上述所有选项。总结缺省值，然后我们返回如下函数:

```
fetch(url, options); 
```

最终结果

```
await this.props.fetch(URL_GOES_HERE, {
   method: 'GET'
   }
 ); 
```

这就产生了一个清晰的获取函数。因为它是通过上下文传递的，所以 fetch 是通过 props 访问的。然而，上述函数对于其他值是完全灵活的。

> 如果需要，可以覆盖默认值，以引入不同的选项。