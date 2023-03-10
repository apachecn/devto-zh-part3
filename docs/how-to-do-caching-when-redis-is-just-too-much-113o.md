# 如何做缓存，当 Redis 太多的时候。

> 原文：<https://dev.to/lucagez/how-to-do-caching-when-redis-is-just-too-much-113o>

**TL；**
*有时候你会需要一些缓存。但是建立专用数据库的开销可能不值得。
我做了 Ricordo，一个微型缓存/备忘录库来解决这个问题。*
https://github.com/lucagez/ricordo[➡](https://github.com/lucagez/ricordo)

我必须开发一个 API。很基本的一个。当找到属于特定用户的路线时，您会得到一个包含他所有产品的 JSON 响应。当找到属于某个产品的路径时，你会得到一个包含其信息的 JSON 文件。

我开始注意到，只有少数产品比其他产品被要求的次数多。所以我开始考虑某种形式的缓存。

这个 API 托管在 digitalocean 的一个 5 美元的实例上。在同一个液滴上托管:

*   NGINX，作为一个反向代理，也用于服务一些静态资产。
*   波斯特格里斯数据库。
*   我的节点 API。

因此，为了缓存少量的 JSON 响应(总计约 50mb)，Redis 在这 5$ droplet 上增加的开销..太多了。

然后我开始考虑将记忆化作为一些小缓存的可行技术。记忆是一种技术，包括存储昂贵计算的结果，并且如果用相同的输入调用该计算，则返回相同的结果。

一个微观的例子:

```
const memo = func => {
  // cache object
  const cache = new Map();

  // Returning function that look up in cache.
  // If nothing is found => a new computation is fired.
  return arg => {
    if (cache.has(arg)) return cache.get(arg);

    // New computation
    const result = func(arg);
    cache.set(arg, result);
    return result;
  };
};

// Super mega expensive function (:
const hello = a => `hello ${a}`;

const cached = memo(hello);

cached('world'); // `hello world` => cache insertion.
cached('world'); // `hello world` => retrieved from cache. 
```

但是，如果我们开始使用它来存储数据库响应会怎么样呢？🤔这就像一个缓存系统。
这样我们可以开始考虑基于函数的缓存。

尽管我们有一些问题。现在，我们的原始缓存实现，
正在存储我们扔给它的所有东西。永远保存密钥和结果。因此，我们将很快耗尽内存，只有当我们的进程结束时，我们的内存才会被释放。
如果我们希望一项服务非常可用，这不是我们想要的。

因此，我们应该用一些类似真实缓存的行为来调整我们的实现，例如 **TTL** 。

生存时间是缓存结果的生命周期。当生命周期结束时，密钥将从我们的缓存中删除。

```
 const memo = (func, ttl) => {
  const cache = new Map();
  return arg => {
    if (cache.has(arg)) return cache.get(arg);

    // Spawning timeout on new insertion
    // => delete key / result after lifespan 
    setTimeout(() => cache.delete(arg), ttl);

    const result = func(arg);
    cache.set(arg, result);
    return result;
  };
}; 
```

稍微好一点，现在我们不会永远存储数万亿个密钥。但不幸的是，我们还有另一个问题😫在高级语言领域，我们不能完全控制如何分配内存。所以，在删除一个键之后，我们不确定 Node.js 是否已经决定
这是释放一些内存的正确时机。
没有什么能阻止我们便宜的 5$ droplet 再次内存不足。

不幸的是，在 JS 中，我们无法确定一个对象在我们的 ram 中占用了多少空间。(至少没有办法不浪费相同数量的存储内存，在这种情况下相当无用)。因此，我们必须依赖于对存储结果将占用多少内存的估计，并决定存储键的数量限制，以防止在达到限制时进一步插入。

```
 const memo = (func, ttl, limit) => {
  const cache = new Map();
  return arg => {
    if (cache.has(arg)) return cache.get(arg);
    const result = func(arg);

    // Now we are waiting for some key deletions before inserting other results.
    if (cache.size < limit) {
      setTimeout(() => cache.delete(arg), ttl);
      cache.set(arg, result);
    }
    return result;
  };
}; 
```

现在，如果我们正确估计了存储结果的大小，我们的 5$ droplet 就不会耗尽内存🎉🎉🎉

但是等等，我们最常用的钥匙呢？我们怎样才能跟踪他们呢？如何才能只存储最常用的 **n** 个项目？
如果我们想要在给定的时间点销毁缓存或单个密钥，该怎么办？

嗯，我面临这些问题，所以我决定做一个小小的 npm 包来解决它们。
希望能让你的微缓存需求少一点痛苦。

这是 github 回购:
[https://github.com/lucagez/ricordo](https://github.com/lucagez/ricordo)

祝大家缓存快乐！✌