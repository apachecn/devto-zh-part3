# 一个简单的“缓存失效”策略，第 2 部分

> 原文：<https://dev.to/vigzmv/a-simple-cache-invalidation-strategy-part-2-4alc>

> "计算机科学中只有两个难题:缓存失效和命名事物."菲尔·卡尔顿

[![](img/27c404e5a0eb072ab541cfdc1d2aa893.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HiHCwwrA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/038pjc1i8afjqb334c6q.png)

这是我上一篇文章的延续，[节点 REST APIs 的简单缓存策略，第 1 部分](https://dev.to/vigzmv/a-simple-caching-strategy-for-node-rest-apis-part-1-72a)我们在文章中谈到了实现一个简单的缓存中间件，它可以总结在上面的流程图中。如果您还没有阅读这篇文章，请在继续之前阅读。那篇文章以一个问题结束，“我们如何才能实现缓存失效？”，我们现在就来探讨这个问题。

#### 好吧，我们再来一次。

**Ques** 。为什么我们需要缓存？
**俺们**。这样用户可以更快地获得数据。
**Ques** 。为什么我们需要缓存失效？
**Ans** 。以便用户获得最新的实时数据。

#### 还有什么是缓存失效？

*高速缓存无效是[计算机系统](https://en.wikipedia.org/wiki/Computer_system)中的一个过程，由此[高速缓存](https://en.wikipedia.org/wiki/Cache_(computing))中的条目被替换或移除。*

*   “替换”:缓存被最近更新的数据替换
*   “已删除”:删除整个缓存。

在这两种方法中，“移除”是最容易实现的，缓存被清空，我们让它用新数据重建。

## 缓存失效策略

这只有两个步骤，但是不同的体系结构之间的实现会有很大的不同。

1.  找到所有可以触发数据更改的来源:数据可以通过 API 端点、周期性任务或隐藏在代码库深处的触发器进行更改。你的工作是找到他们
2.  添加一个在数据更改后清除/更新缓存的方法。就这么简单。😅

同样，具体的实现可能会有所不同

1.  在数据库模型的底层添加一个缓存清除方法，比如，`$afterUpdate`。
2.  将它添加到每个更改数据的方法中。这完全取决于应用程序的复杂性。

在这篇文章中，我们将扩展我们的缓存中间件来处理失效，让我们看看它是如何工作的。**这个例子假设改变资源的任何数据的唯一方法是通过进行 POST 请求**。我们将在我们的中间件中添加一个名为`clear`的新方法。

```
// middlewares/cache.js

const NodeCache = require('node-cache')
const cache = new NodeCache({ stdTTL: 5 * 60 }) 
function getUrlFromRequest(req) {
    ...
}

function set(req, res, next) {
    ...  
}

function get(req, res, next) {
    ...
}

+ function clear(req, res, next) {
+   cache.keys(function(err, keys) {
+       if (!err) {
+           let resourceUrl = req.baseUrl;
+           const resourceKeys = keys.filter(k => k.includes(resourceUrl));
+           cache.del(resourceKeys);
+       }
+   });
+   return next();
+ } 
module.exports = { get, set, clear } 
```

并在我们的路线中使用

```
// products/routes.js

router.get(
    ...
)

router.post(
    '/',
    productsController.create,
    cache.clear, // 👈
    responseHandler
) 
```

第一

每当发出一个`POST`请求时，数据就会发生变化，因此我们触发一个缓存清除，以便在下一个`GET`请求到来时可以重建缓存。

**在`cache.clear`到底发生了什么？**T3】

```
// middlewares/cache.js

...
...

function clear(req, res, next) {
    cache.keys(function(err, keys) {
        if (!err) {
            // again, it depends on your application architecture,
            // how you would retrive and clear the cache that needs to be cleared.
            // You may use query path, query params or anything. 
            let resourceUrl = req.baseUrl;
            const resourceKeys = keys.filter(k => k.includes(resourceUrl));

            cache.del(resourceKeys);
        }
    });
    return next();
} 
```

*   `cache.keys`返回所有存储的缓存键，
*   `req.baseUrl`具有请求的基路径，例如`'products'`
*   获取所有将基本路径作为子串的键。(记得吗？键只是指向资源的`GET`URL)
*   `cache.del`清除键指向的缓存。

**例如**，如果我们的缓存中有这样的键值

*   [https://api.com/products?sort=date&include =卖家& top=25](https://api.com/products?sort=date&include=sellers&top=25)
*   [https://api.com/products?sort=cost&包括=【卖家，销售】](https://api.com/products?sort=cost&include=%5Bsellers,sales%5D)

对`/products/`的`POST`请求将清除所有这些👆缓存，当新的`GET`请求进来时，它们将被重建。

对于我的例子，简单地清除所有在`POST`请求的基本路径范围内有关键字的缓存就可以了。

通过这种设置，理论上，我们可以将我们的缓存 TTL 设置为无穷大，因为数据的每次更改都会清除它，并且缓存将总是具有最新的数据。但是为了理智起见，我们将 TTL 保持在 15 分钟。现在，我们的用户可以更快地获得最新数据。

今天到此为止。编码快乐！

在推特 | [Github](https://github.com/vigzmv) 上关注我，我制作并发布很酷的东西。👨‍💻