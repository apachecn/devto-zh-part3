# 为什么浏览器缓存这么难？

> 原文：<https://dev.to/trezy/why-is-browser-caching-so-hard-12d0>

## 浏览器缓存是最好的照顾用户的方式之一，同时也加快了网站的速度…为什么会这么难呢？

**本文最初发表于 2014 年 10 月 19 日的[Trezy.com](https://trezy.com/blog/why-is-browser-caching-so-hard)。**

浏览器缓存是一件神奇的事情。每当您访问一个网站时，组成该网站的文件都会被浏览器下载到您的计算机上，然后浏览器会使用这些文件为您呈现这些文件。这需要时间和宝贵的带宽，尤其是在较慢的连接上。然而，下次你访问同一个站点时，渲染它所需的文件已经下载到你的计算机上了。您的浏览器将使用它已经拥有的文件来呈现站点，避免您浪费带宽并等待文件下载。

现在，这就是在一个奇妙的、神奇的童话世界里发生的事情，所有的 web 开发人员做正确的事情，并确保在他们的网站上设置浏览器缓存。不幸的是，这种情况很少发生。本着编造统计数据的精神，我估计大约有 30–90%的网站不仅仅利用浏览器缓存，他们还积极地反对它。
😞

看，有几种方法可以实现浏览器缓存。你可以使用所谓的`cache-control`头，它告诉浏览器它可以保存文件多长时间。如果你看看我自己的网站，你可以看到我从谷歌下载的谷歌分析脚本有这个标题集。

[![The cache-control header has a max-age of 7200 seconds, or 2 hours.](img/81a7932772bc7701e31c99c2d98ff676.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KECpPi4P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2zx6rh0vi0aepov91lej.png)

两个小时还不错。如果我在接下来的两个小时内再次访问我的网站，我的浏览器仍然会使用我刚刚下载的文件版本。最酷的是，因为它是由谷歌而不是我的服务器提供的，这个文件将被每个使用它的网站缓存，包括我的网站！

然而，如果谷歌在接下来的两个小时内更新了那个文件，我将不会得到那些改变。
😞😞

这也是很多网站使用一种叫做**缓存破坏技术的一个很好的例子。**缓存破坏是你欺骗浏览器，让它认为这个谷歌分析脚本是一个不同于它之前下载的文件，保证用户**总是**得到最新版本的文件。开发人员通过在文件名末尾添加类似于`?timestamp=2017-10-19T13:04:29.423Z`的东西来实现这一点。它只是一个使用当前日期和时间的参数，所以浏览器总是认为这个文件是不同的，因此总是下载一个新版本。现在，每次我去那个网站，我都必须下载那个文件的新副本。
😞😞😞

## 那么……有没有更好的办法？

还记得我说过有几种实现浏览器缓存的方法吗？先说那第二个，叫 ETags。ETags 是服务器在浏览器第一次尝试检索文件时生成并发送的东西。这是一个对我的网站提供的文件的新请求的例子。

[![Notice there is no expires header. The cache-control header has a max-age of 0, which somewhat unintuitively means feel free to keep this file forever.](img/83df11eaa54bc6507b8038979109b9b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5VAQH3dP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nxvcfv7pmijen7yx9mvw.png)

这是我的 logo，有一个`etag`头集。这个头包含了我正在下载的文件的计算散列。服务器为我创建了那个散列，并把它和文件一起发送了上去。现在让我们看看我第二次请求该文件的情况。

[![The ETags match!](img/fbfe967b49bcd015b22794888bdfffa6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ePjDqHtk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g3xrc58yax7wnw7c6cs2.png)

因为浏览器在第一次下载这个文件时收到了一个`etag`头，所以它确保在`if-none-match`头中将 ETag 发送回服务器。服务器会将该 ETag 与它为该文件版本计算的值进行比较。如果它们*不匹配*，服务器将向浏览器发送文件的新副本。

然而，如果他们*做*匹配，它根本不会发回文件。相反，它会发回一个`304 Not Modified`响应，告诉浏览器一切都没有改变。Trezy.com 服务器为我节省了时间和带宽。但是，它到底拯救了我多少？

在对[Trezy.com](https://trezy.com)的新访问中，网站下载了 *781KB 的文件*，用了大约 *1.34 秒完成*。这只是为了我非常小的网站。对于您经常访问的其他网站，您可以想象这些数字会大得多。

第二次，对我的网站进行缓存友好的访问，虽然它只下载了 *46.1KB 的文件*，花了 *1.05 秒完成*。这相当于*节省了 94%的带宽*，节省了 22%的响应时间。再说一次，这只在我非常非常小的网站上。想象一下，在脸书或 Twitter 这样的大型网站上，这些节省会是什么样子！
🤔

* * *

## 让我们来看看一些实现

我们将看看我在我的网站上使用的非常简单的 ETags 实现，它运行在一个 [Koa.js](https://koajs.com) 服务器上。下面是`koa-etag`的核心功能。它负责确保服务器的响应计算了 ETags 并附加到响应中。

```
function etag(options) {
  return function etag(ctx, next) {
    return next()
      // `getResponseEntity` checks to see if the response has a body, and
      // stringifies it depending on what kind of body it has (JSON, HTML, 
      // image, etc)
      .then(() => getResponseEntity(ctx))

      // `setEtag` calculates the ETag from the response body if it exists,
      // then sets that ETag as a header
      .then(entity => setEtag(ctx, entity, options));
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，这并不是我们需要做的全部。来自服务器的响应将仍然包含其余的内容，它将用一个`200 OK`来响应，告诉浏览器它基本上正在获取一个新文件。这是拼图中缺失的部分。

```
function conditional() {
  return function conditional(ctx, next) {
    return next().then(() => {
      if (ctx.fresh) {
        ctx.status = 304;
        ctx.body = null;
      }
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这是怎么回事？`ctx.fresh`是一个特定于 Koa.js 的属性，但它主要是检查新旧 ETags 是否匹配。这个中间件`koa-conditonal-get`使用这个属性来决定如何继续。如果它们匹配，浏览器拥有的信息是`fresh`，我们不需要发送任何新信息，所以我们将状态设置为那个漂亮的小`304 Not Modified`，并删除响应体，消除了通过网络发送谁知道有多少官样文章的需要。如果电子标签不符合威尔·史密斯的标准，我们将需要发送新的内容。

* * *

## 让它真实！

如果你所在的网站中有 30–90%没有实现浏览器缓存，那么现在是修复它的最佳时机！这是一个愚蠢的简单的 ETags 实现，可能无法满足很多系统的需求，但是这是一个很好的开始。也不仅仅是网站！API 也可以利用 ETags 使开发人员的生活变得更容易。如果你对可伸缩性有争议，我会继续向你指出，聪明的先生或夫人，Github 在他们的 API 中实现了 ETags，所以。
😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁😁