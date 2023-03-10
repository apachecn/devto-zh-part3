# 懒加载能做到？

> 原文：<https://dev.to/gparlakov/lazy-loading-can-do-that-3gk9>

<figure>[![](img/3a0b93cdd306430236d7c1ed1b1c44bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MOE4Dr-J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/592/1%2AI7IBTjR9RETcv27qtqrCVA.png) 

<figcaption>迅疾消瘦。</figcaption>

</figure>

## 优化角度

2019 年 6 月 7 日更新:关于共享模块的说法似乎不正确。Angular 编译器实际上会对未使用的组件进行树抖动，而不会对未使用的提供者(服务)进行树抖动。详细讨论见[此处](https://medium.com/@gparlakov/after-a-bit-of-research-heres-the-result-in-this-github-repo-1804df5e8373)

> **优化**主要是为了快速的用户体验！我们将看看我们可以做什么类型的延迟加载，以及如何在优化角度的同时测量我们的结果。

我们将对进行**优化，主要是为了快速的用户体验。这意味着**更小的包**和**不下载**不需要的**代码**。我们希望**将用户**需要的**和**不需要的**字节**发送给**。这样，我们的用户将会看到一个快速反应的网站。用户已经开始期待这一点。****

免责声明:**这里不讨论服务器端渲染**。我们将尝试优化在浏览器中交付和评估的 javascript。

我们将看看我们可以做什么类型的延迟加载，以及如何在优化角度的同时测量我们的结果。计划是这样的:我们先测量。我们会创造假设。然后我们会应用一些优化，衡量，看看假设是否正确。

我已经创建了一个小的示例应用程序来进行优化。在 https://github.com/gparlakov/optimizing-angular 找到它

#### 测量:

我们将使用这些工具:

[Webpack 捆绑包分析器](https://www.npmjs.com/package/webpack-bundle-analyzer)。这是一个工具，它从`ng build --stats-json`命令获取输出，并在一个非常漂亮的图形界面中呈现出来。它使用输出中的`stats.json`文件。我们可以看到我们生产了多少个包，什么去了哪里，以及它们的大小(Stat — [raw](https://www.npmjs.com/package/webpack-bundle-analyzer#stat) ，Parsed—[processed](https://www.npmjs.com/package/webpack-bundle-analyzer#stat)[*like after minimization】*和 [Gzipped](https://www.npmjs.com/package/webpack-bundle-analyzer#gzip) )。这里有一个例子:

ng build-prod-stats-JSON

web pack-bundle-analyzer dist \ stats . JSON

<figure>[![](img/806a592ef199c0a926ea1391c252c619.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_yjfIz1G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aex-MzdA2u5TBGAbFsKlAAw.png)

<figcaption>web pack-bundle-analyzer 工具报告的截图。1.左上角—选中了“显示内容…”复选框能够 2。顶部中间—主束。3.右下角—应用程序的组件。</figcaption>

</figure>

[Webpack 捆绑包分析器](https://gparlakov.github.io/optimizing-angular/stats/v1_eager_base_line/report.html)

在截图中(或者如果你访问上面的链接)，你会看到 main bundle 是迄今为止最大的——解析了 1.17MB(占用了多少磁盘空间)和 328KB Gzipped(估计**)。它包括 angular 运行时和库(@@angular - core.js，forms.js)，ethers*和 bip39*，primeng/components-我们的 UI 组件和一堆更小和更大的 js 文件都捆绑在一起-包括我们的应用程序定制代码所在的 src。这就回答了这个问题——为了绘制我们的应用程序，我们需要向浏览器发送多少 javascript 代码。

这些是用于加密的库。我选择它们是因为我知道它们占地面积很大。它们将有助于展示优化的一个特定方面。

[chrome dev**网络**工具:](https://developers.google.com/web/tools/chrome-devtools/network/)

<figure>[![](img/d5d6af9ed326efca402988bb2a1e3299.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bFP5NQqM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAdNolpqiSA0CErJDoSBnwQ.png) 

<figcaption>我们希望为最终用户优化加载时间。</figcaption>

</figure>

它显示了资源(在本例中过滤为 JS——注意上面过滤栏中选择的 JS)加载的数量和速度。

貌似这已经是一个快速加载的 app 了。但是考虑到只有三页内容，我认为我们可以做得更好。

* * web pack-bundle-analyzer 报告(显示 328KB main)和网络工具(显示 373 KB)之间有些不一致。因为这是由 angular development server(ng serve-prod)提供的，所以我猜不同版本的 gzip 是原因。无论如何——我们将把报告中看到的**包**的**大小**作为**指示**到**实际字节**被**传输**，并考虑到具体的实现(nginx、IIS、Apache 等)。)在挤压我们的包时可能会导致尺寸上的微小差异。

[和 chrome 开发工具**性能**工具](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/):

<figure>[![](img/64d560ff2f896f04278398becf5df7a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hiYdUiXB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASpE1fiXIQRMFurLtqRfQ1Q.png) 

<figcaption>来自 Chrome 性能工具的截图。我们可以看到网络的统计数据(顶部)和我们的主线程正在做什么(底部)。</figcaption>

</figure>

从上面的表演镜头中，我们可以看到 **main.d72a…js** bundle 占用了网络的大部分空间，并且花费了大量的时间进行评估。

当**测量**时，注意使用**隐姓埋名**窗口(在 Chrome 和其他任何浏览器中)，或者禁用任何可能干扰的扩展。在我的例子中，我有两个扩展，增加了大约 700 ms 的加载时间。所以我所有的测量都是匿名的。

所有测量都是在 ng build 或 ng serve 命令中设置了- prod 标志的情况下完成的。否则，我们将得到源地图、未混合的 javascript 和更多的包。

也就是说，这是我们的基线:

#### 基线

好了，我们有了我们的基线:

**1.17MB** 大小的**main . js**(328k gzipped)

**830 ms**加载时间。

**217ms** 评测 main.js

现在到了有趣的部分——优化。

#### 懒载

假设:延迟加载将有助于加快初始引导时间，加快用户体验。

惰性加载是一种技术，只加载需要的模块，其余的留在服务器上，直到需要时再加载。比如我们的 app 有**首页**模块、**登录**模块、**个人**模块。每一个登陆首页的人都需要**首页**模块。但并不是每个人都会登录或使用个人资料页面。因此登录和个人模块将保留在服务器上，直到用户真正请求使用它们。这样，我们可以保持应用程序快速加载，并保存用户的数据计划。(同样，这个示例应用程序很小，有点做作，但相信我，我说得有道理)。

最初，我们在应用程序开始时就急切地加载了所有的模块和组件。因此，构建只创建了一个包——main . js——并将所有东西——供应商代码和我们的定制代码——连接到其中。我们最初的 app-routing.module 和 app.module 如下所示: