# 我如何重构我的代码

> 原文：<https://dev.to/pancy/how-i-refactor-my-code-47cc>

[![constructor guy](img/244db2cf558bf6aad6084aad9ca6b2a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZdjptNCm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1760/1%2AZu_H4su-jf6O-trZR41X8w.jpeg)

重构代码对任何开发人员来说都是非常基础的工作。然而，我遇到的关于这一点的深入讨论相对较少。

这篇博文发生在今天早上我重构 JavaScript 代码之后。它只持续了不到 30 分钟，但却让我兴奋不已，重新开始在 Medium 上写作。

## 让我们开始大重构的故事吧！

首先，我将这两个 fetch 函数分散在我的代码库中，它们的名称略有不同，我希望将它们重构为一个可重用函数的模块。这里只是其中的两个:

```
async function postLoginData(data) {
  const loginUrl = `${apiBaseUrl}/login`;
  let response = await fetch(loginUrl, {
    method: "POST",
    mode: "cors",
    cache: "no-cache",
    credentials: "same-origin",
    headers: {
      "Content-Type": "application/json; charset=utf-8",
    },
    redirect: "follow",
    referrer: "no-referrer",
    body: JSON.stringify(data),
  });
  return response;
}

// Get the user's data based on user id.
async function getUser(userId) {
  const userUrl = `${apiBaseUrl}/users/${userId}`;
  let response = await fetch(userUrl, {
    method: "GET",
    mode: "cors",
    cache: "no-cache",
    credentials: "same-origin",
    headers: {
      "Content-Type": "application/json; charset=utf-8",
    },
    redirect: "follow",
    referrer: "no-referrer",
  });
  return response;
} 
```

Enter fullscreen mode Exit fullscreen mode

我不是一个极端干燥的倡导者，但这感觉很麻烦。每一个函数所做的都很少，仅仅通过获取它所包装的内容就可以实现。除了封装端点 URL 和方法属性之外，这两者看起来完全一样，应该可以在整个代码库中重用。

## 功能应该尽可能的纯粹

对于一个函数，我的首要标准是尽可能重构为纯函数。纯洁意味着可重复使用。如果它需要改变任何共享状态，它可能是一个方法的候选者。这使得功能易于测试和重用。名字类似于`postLoginData`的函数违反了这一点。这里有几个不用考虑实现就能重构它的方法:

*   `user.login()`
*   `login(user)`
*   `post(loginUrl, user)`

上面的列表从最不通用到高度可重用排序。实际上，前两者具有相同的普遍性。只有最后一个是可重复使用的，这就是我想要的。

现在，你可以看到我的两个函数是多么令人讨厌。有时候，你戴着不同的帽子，优先考虑不同的事情。只要我们偶尔清理一下，匆匆忙忙地让事情运转起来是没问题的。

## 如何证明重构的合理性

为了决定某样东西是否应该重构，我会考虑为它创建一个函数的意图和价值。

例如，一个“发送”数据的函数和另一个“获取”数据的函数有着根本不同的意图，尽管在实现上只有很小的差异。意图非常明显，足以证明创建两个函数是正确的。

然而，考虑到函数的通用性下降，将任意 URL 包装在函数中(例如，登录 API 端点)，然后命名函数`postLoginData`并不会给函数增加太多价值。URL 除了是一行字符串之外，还应该是调用者的“故事”。想象一个有油画颜料、调色板和画笔的艺术家。艺术家要画的应该是艺术家的故事。调色板以及颜料和画笔的集合应该提供支持主题的变体。你能想象出一套画海洋场景的颜料吗？这是明智的。现在画一艘船怎么样？没那么容易。这个主题太具体了，无法封装。

事不宜迟，下面是第一次重构尝试:

```
const baseConfig = {
  mode: "cors",
  cache: "no-cache",
  credentials: "same-origin",
  headers: {
    "Content-Type": "application/json; charset=utf-8", 
  },
  redirect: "follow",
  referrer: "no-referrer",
};

// Configurable POST with predefined config
async function post(uri, data, config = {}) {
  config = Object.assign({
    method: "POST",
    body: JSON.stringify(data),
    ...baseConfig,
  }, config);
  return await fetch(uri, config)
}

// Configurable GET with predefined config
async function get(uri, config = {}) {
  config = Object.assign({
    method: "GET",
    ...baseConfig,
  }, config);
  return await fetch(uri, config);
}

export {get, post}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，重复的配置对象属性被重构为一个常量`baseConfig`，这看起来更清晰了。此外，我为每个函数添加了一个可选的`parameterconfig`,使其可以从外部进行配置。`Object.assign`用于合并自定义配置和基本配置(你也可以使用扩展操作符)。

我们还可以看到物体在运动中扩散。在这一点上，我很高兴，但利用空闲时间，我决定看看我是否能完成更多的事情。下面是最后的尝试:

```
const baseConfig = {
  mode: "cors",
  cache: "no-cache",
  credentials: "same-origin",
  headers: {
    "Content-Type": "application/json; charset=utf-8",
  },
  redirect: "follow",
  referrer: "no-referrer",
};

const send = (method, payload) => (
  async function(uri, config) {
    // Create an array of source config objects to be merged.
    let sources = [config];
    if (method === "POST") {
      sources.push({ body: JSON.stringify(payload) });
    }
    config = Object.assign({
      method: method,
      ...baseConfig,
    }, ...sources);

    return await fetch(uri, config);
  }
);

const get = (uri, config = {}) => (
  send("GET")(uri, config)
);

const post = (uri, data, config = {}) => (
  send("POST", data)(uri, config)
);

export {get, post}; 
```

Enter fullscreen mode Exit fullscreen mode

我个人最喜欢这个版本，因为`get`和`post`函数是新创建的`send`函数的非常薄的包装(它没有被导出，因为我想保持它的私有)。这使得后者成为调试的单一点，如果 bug 随后持续存在的话，这是必然的。

重构是一件棘手的事情，不是因为它很难，而是因为它需要更深入的设计思考，没有绝对的对错。毫无疑问，你不会让每个人都满意。令人惊讶的是，重构代码使其可重用会让一些人失去兴趣，尤其是当权衡远大于收益的时候。因此，平衡是需要努力的。还有其他一些因素，例如命名约定和函数参数，它们可以帮助提高可访问性，应该总是很难解决。然而，最终，请记住您应该首先为自己进行重构，因为您更有可能与您编写的代码进行交互。

原贴[此处](https://medium.com/@jochasinga/how-i-refactor-my-code-561aa9132045)