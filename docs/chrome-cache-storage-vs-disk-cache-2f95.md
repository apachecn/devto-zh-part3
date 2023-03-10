# Chrome:缓存存储与磁盘缓存

> 原文：<https://dev.to/dnesteryuk/chrome-cache-storage-vs-disk-cache-2f95>

最有可能的是，你听说过[缓存存储 API](https://developer.mozilla.org/en-US/docs/Web/API/Cache) 和[服务工作者](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)，它们可能被用于缓存资源并在以后从缓存中提供服务。其中一个用例是在用户需要资源之前预取资源。

我一直在做[一个项目](https://github.com/sirko-io/engine)，它实现了所描述的用例。几周前，Stefan 为[创建了一个任务](https://github.com/sirko-io/engine/issues/45)，他描述了一个观察结果，即在某些情况下，从缓存中交付资产的速度很低。所以，我决定验证一下。

我创建了[演示](https://gist.github.com/dnesteryuk/eff23819e44ec61c842a16d0237b8c96)来比较缓存存储和磁盘缓存。`index.html`需要显示 N 张图片。有一个选项可以预先缓存它们，然后嵌入到页面中。`sw.js`查看缓存，如果资源在那里，就从缓存中得到服务，否则，就正常下载。

### 测试条件

测试只在 Chrome 上进行。如果有足够的兴趣，我也可能在 Firefox 中执行它们。所有的图片都有相同的大小，但是不同的名字，使得浏览器一次又一次的请求它们。下面你会看到 10 次尝试的最佳结果。

Chrome Dev tools 为每个资源提供计时。

[![](img/7239a8548cd72179a210bf9044cdb4dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vKnOgE0i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8fd39i0kafm7gsn4tofb.png)

关于所有加载资源的信息可以作为[HAR 文件](https://en.wikipedia.org/wiki/.har)下载。那么可以使用任何语言/工具来分析提取的信息。在每一次尝试中，我都在查看加载所有图像的时间。所以，当你遇到`min`、`max`或者`mean`的时候，我指的是所有图像加载的时间。

### 测试#1: 100 大图

对于这个测试，图像大小为 1.5 Mb。一般来说，不太可能有网站会加载那么多的图片。更多的是好奇。

#### 缓存存储

[![](img/465fb543811d7a2dbead5d15d6ce49a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kqJO3ldB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jm0ifmg61wgr6n976q8r.png)

大多数情况下，浏览器花时间下载图片。浏览器如何开始处理请求没有清晰的模式。

正如我提到的，进行了 10 次尝试，下面是关于它们的统计数据:

*   **分钟:** 514.93 毫秒(如上图所示)
*   **平均值:** 755.43 毫秒
*   最大值: 1031.07 毫秒

#### 磁盘缓存

[![](img/6d82dd74ce5c4a82cbeff7d18b316fb0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0luHuIPp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xo3t2uhywntc73n4y99h.png)

在这种情况下，下载操作并没有花费太多时间，但是图像需要排队。我们甚至可以看到浏览器是如何获取图像进行处理的(一批大约 6 张图像)。

统计大约 10 次尝试:

*   **分钟:** 646.73 毫秒(如上图所示)
*   **平均值:** 1050.81 毫秒
*   最大值: 1450.36 毫秒

在这次测试中，高速缓存显然是赢家。

### 测试#2: 30 小图片

对于这个测试，图像大小为 31.3 Kb。这种情况比前一种更有可能是真实的。我使用图像进行测试，但它可能是不同的资产(javascript 文件，字体，图像，css 文件等)，所以一些网站可能会在一个页面上加载 30 个资产。

#### 缓存存储

[![](img/df038f2db5b8a850cff1cdbe5437afe4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FIHlERFF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/is5jsgpu1dp0qdw3agac.png)

下载不再是问题，但等待却成了问题。

统计大约 10 次尝试:

*   **分钟:** 26.22 毫秒(如上图所示)
*   **平均值:** 34.1 毫秒
*   最大值: 40.84 毫秒

#### 磁盘缓存

[![](img/93ffd6e8f973b09caf7d9b0d8794ee3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_hps75iW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o962cc85pnnsawu85d8g.png)

同样，排队操作比任何其他操作花费的时间都多。

统计大约 10 次尝试:

*   **分钟:** 15.3 毫秒(如上图所示)
*   **平均值:** 22.89 毫秒
*   最大值: 28.97 毫秒

现在，磁盘缓存是赢家。

### 测试#3: 100 张小图片

再次，这几乎是不真实的情况，但我好奇为什么缓存存储在第一次测试中更快。可能是图像的数量或图像的大小。因此，我从之前的测试中提取图像，并加载 100 次。

#### 缓存存储

[![](img/096b0877682426fc862ba84d955a73f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---qNxfu3r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vkerdaunlw2zbky65aqn.png)

再次，等待操作在这里占主导地位。

*   **分钟:** 65.5 毫秒(如上图所示)
*   **平均值:** 78.54 毫秒
*   最大值: 90.51 毫秒

#### 磁盘缓存

[![](img/57683a3676c17c699a15ff139094c6d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nOO44c8j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/06os6ylb1yrybszl37jk.png)

同样，图表显示浏览器获取了一批 5-6 个图像，并行加载它们，然后获取另一批图像。

*   **分钟:** 55.44 毫秒(如上图所示)
*   **平均值:** 101.84 毫秒
*   最大值: 142.45 毫秒

通过比较，平均缓存存储再次胜出。所以，我认为它更多的是关于并行服务请求的能力。

## 总结起来

即使测试是在本地主机上进行的，不同的测试结果也是不同的。磁盘缓存在交付少量资产方面稍好，缓存存储在交付大量资产方面更好。在某种程度上，比较缓存存储和磁盘缓存有点不公平，前者有更广泛的用途，开发人员可以访问 API 来控制它。无论如何，缓存存储并不像任务打开时看起来的那样慢。