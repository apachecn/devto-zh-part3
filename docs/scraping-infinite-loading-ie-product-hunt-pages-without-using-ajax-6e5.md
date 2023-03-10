# 抓取无限加载(即:产品搜索)页面！不用 ajax！

> 原文：<https://dev.to/vanila-io/scraping-infinite-loading-ie-product-hunt-pages-without-using-ajax-6e5>

如果你喜欢构建网页抓取器，你就会知道抓取无限加载的网页有多难。你在谷歌上看到的大多数搜索结果都集中在两种方法上。

1.  在网络选项卡上找到 ajax，尝试从它那里刮。
2.  使用`document.body.scrollHeight`、`window.scrollTo`和一些 for 循环的组合。![](img/a8f5f4aaf4510ff9e2438e4b874863b0.png)

不幸的是，它们中的大多数都不能很好地处理延迟加载的图像，或者由**平滑滚动**触发无限加载的页面。在许多情况下，摆弄网络标签似乎会适得其反，并且很容易在一些网站上被触发为机器人。

几年来，我一直在思考这个问题的解决方案。尝试了所有不同的方法，但都失望了，因为网站之间的差异很大。

最后，当我在 stackoverflow 上更新[这个](https://stackoverflow.com/a/52671691/6161265)和[这个](https://stackoverflow.com/a/56349839/6161265)时，它踢了我一脚。请随意探索它们。

这是我们今天将构建的小预览。这是一个一分钟的视频(对不起，没有音频)。
[https://www.youtube.com/embed/hUNeD4TMwbk](https://www.youtube.com/embed/hUNeD4TMwbk)

# 案例:

你需要从产品搜索中收集 100 个结果。结果应该包含以下结构的文章标题和图像 url。一旦达到极限或没有剩余元素，它必须停止刮擦。

```
[  {  "title":  "Some product title",  "img":  "https://ph-files.imgix.net/123456-abcdefghijkl"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用名为`window.scrollTo`的可靠方法，但不使用`document.body.scrollHeight`。

# 解:

PS:如果要读码，跳转到[最终码](#final-code)。

我们要做的是，

*   我们将提取选择器(显然🤷).
*   然后，我们将在页面上找到该选择器的第一个元素。如果没有元素，我们就不会继续。
*   将元素滚动到视图中。
*   **可选！**等待几毫秒，让它加载图像，反之亦然。
*   从元素中提取信息。
*   **重要！**从 dom 中移除元素。
*   **重要！**滚动到页面顶部。
*   执行下一个元素，或者在达到限制时停止。

标记为**重要**的步骤是关键。它们将触发页面上的滚动事件，甚至不需要像其他人使用`document.body.scrollHeight`等等那样手动滚动。

好了，现在你知道了解决方案，你可以从上面的视频中编码剩下的部分。

[![](img/b1ef4ba3146cfa974b63af9faee842dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EYHKnC2t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2fm7cm.jpg)

我开玩笑的😅！下面是步骤！

## 提取选择器

你可能做过很多次了。但是这里有一个简短的回顾。我不会在这里直接写选择器，因为在你读这篇文章的时候它们可能会改变。
打开 chrome，加载 producthunt 页面。然后右击标题。

[![](img/48b963c9dc7f5ae64b3892fb772f0d52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BmtG-iBD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Qg2Yh1L.png)

现在选择任何一个类。下一步我们会找到合适的。

[![](img/e509d36db8995056010414f5a3df0fea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CAs0aquo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/cd06ERt.png)

在控制台上记下类名。它会做一个即时评估，所以你会马上知道选择器是否正确。

[![](img/b7a70510752610f0fce4031e0e663ee4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nnQdOGQN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/4VVFsws.png)

因为我们有 27 个结果，我们可能在正确的道路上。因为第一次加载页面时，大约会有 20-30 个结果。

好了，接下来我们可以提取图像的选择器。

幸运的是，图像选择器更加简单，因为我们有一个很好的数据属性。

[![](img/3e183efafe2dbd80fff01678b517dbf5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aSdaNujT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ePZ7CBS.png)

但是，如果稍微调整一下选择器，您会看到 27 个产品中有 25 个结果。这意味着它没有加载最后两个图像。

[![](img/2a6e9f3389dcf5af95b2198edb23d634.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MJxufJRV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/JklwfUQ.png)

如果您现在放弃这个页面，您将得到 25 个合适的结果。另外，我提取了每个产品列表的父元素。

[![](img/5166e1d5a9434794c95fb33e7a36b310.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u2MPqJCw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/m3fQBHQ.png)

现在我看到一些奇怪的东西，它说 34 个结果。这意味着它甚至没有加载最后 7 个结果。甚至不是标题。它在那里，但目前没有加载。

最后，我们有三个选择器。

*   产品条目(可选):`div.white_09016 ul li`
*   Title: `.title_9ddaf`
*   图片:`[data-test="post-thumbnail"] img`

这些选择器可以随时改变，因为它是一个基于 react 的网站。

## 刮取数据

你可以在浏览器控制台上执行这些代码，或者使用一些脚本/库 ie: puppeteer 有一个执行函数的`page.evaluate`方法。我将使用 [Scratch JS](https://chrome.google.com/webstore/detail/scratch-js/alploljligeomonipppgaahpkenfnfkn) 来运行页面上的代码。

**抓取单个产品**
让我们创建一个名为`scrollAndExtract`的异步函数，它接受名为`selector`和`leaf`的两个参数。叶是`innerText`和`src`等。

我们需要`async`,因为我们将在函数中使用延迟来展示。

```
const scrollAndExtract = async (selector, leaf) => {
  const element = document.querySelector(selector);
  if (element) {
    element.scrollIntoView();
    return element[leaf];
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们运行它，

```
scrollAndExtract(".title_9ddaf", "innerText").then(console.log);
scrollAndExtract('[data-test="post-thumbnail"] img', "src").then(console.log); 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/a242dd34c1f3010b952eed7cbdf06262.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CcTJ10b6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/87fq1Ez.png)

酷！我们得到了第一个标题和图片 url。

**滚动并移除元素**

接下来，我们将从视图中删除该元素。我们可以通过添加另一个参数并稍微调整我们的函数来以更简单的方式做到这一点。

我们来添加一个`remove`参数。如果提供了，我们将删除该元素，而不是提取数据。

```
const scrollAndExtract = async (selector, leaf, remove) => {
  const element = document.querySelector(selector);
  if (element) {
    element.scrollIntoView();
    if (remove) return element.remove(); // <-- Remove and exit
    return element[leaf];
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们来测试一下，

```
scrollAndExtract(".title_9ddaf", "innerText").then(() => {
  scrollAndExtract(".title_9ddaf", null, true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

产品名称消失了，

[![](img/db189dba40427d4d13f200af6780ffae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pe_w9u89--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Ktxgd56.png)

**刮图像**

现在我们也可以用类似的方式抓取图像。

```
scrollAndExtract('[data-test="post-thumbnail"] img', "src").then(() => {
  scrollAndExtract('[data-test="post-thumbnail"] img', "src", true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这将从图像中提取 src 属性。

它们都可以合并成一个返回对象的函数。我们可以稍后将其推送到一个数组中。

```
async function extractor() {
  const title = await scrollAndExtract(".title_9ddaf", "innerText");
  await scrollAndExtract(".title_9ddaf", null, true);

  const img = await scrollAndExtract('[data-test="post-thumbnail"] img', "src");
  await scrollAndExtract('[data-test="post-thumbnail"] img', null, true);

  return { title, img };
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来测试一下，

```
extractor().then(console.log); 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/7c5e6cbcbff8be573294c3ce45eef47f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DGMbq_TG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/YsZ64Fw.png)

**可选:移除标题和图像的父容器**
让我们在抓取标题后移除父元素。

这是可选的，因为即使没有它，逻辑也能工作。但是这将为我们节省一些视窗空间和内存，因为我们删除了 dom 元素。

我们可以删除父容器，不必担心删除图像或标题元素，因为它也会被删除。

```
async function extractor() {
  const title = await scrollAndExtract(".title_9ddaf", "innerText");
  const img = await scrollAndExtract('[data-test="post-thumbnail"] img', "src");

  // remove the parent here
  await scrollAndExtract("div.white_09016 ul li", null, true);
  return { title, img };
} 
```

Enter fullscreen mode Exit fullscreen mode

它应该可以完美的工作，

[![](img/009a5010a029cf11742654eab77057bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wxsHiran--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/jFyI3rb.png)

**遍历 100 个元素**
我们不会使用传统的 for 循环。我们将使用递归来代替。

让我们创建另一个函数来逐个遍历元素。我们将结果存储在一个结果数组中。

```
const products = [];
async function hundredProducts() {
  if (products.length < 100) {
    const data = await extractor();
    if (!data.title || !data.img) return null;

    products.push(data);
    return hundredProducts();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将为我们抓取前一百个元素。不仅如此，如果提取器没有结果，它将停止循环。

我们可以查看`products`数组来获取我们的结果。

```
hundredProducts().then(() => console.log(products)); 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/3177a37bf84453706190c40087d0e6d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jRjhY2bd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/CXFoJqD.png)

然后砰！

我们得到了 7 个结果！

[![](img/3d8977fc32d38e6e7cba136d2f2eb9f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aYh0aMz0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/gDvnQEu.png)

等等愤怒？

**给延迟加载的产品数据加上图片**
如你所见，我们只得到 7 个结果。这是因为我们告诉它停止循环，如果没有图像/标题。它滚动得太快，无法触发任何滚动事件和加载新数据。

让我们使用一个简单的延迟函数，它会在运行循环之前等待一段时间。

```
const delay = d => new Promise(r => setTimeout(r, d)); 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们还可以滚动到页面顶部。

```
const products = [];
async function hundredProducts() {
  if (products.length < 100) {
    // Let's wait 0.5 seconds before moving to next one
    await delay(500);
    // also trigger a scroll event just in case
    window.scrollTo(0, 0);

    const data = await extractor();
    if (!data.title || !data.img) return null;

    products.push(data);
    return hundredProducts();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/58622fc583ddeedbfb91bd2bde743185.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QpoPJ2Nt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/bbxF2lp.png)

**最终结果**
好吧！这是一个很长的帖子，现在我们有了一个脚本和逻辑来抓取像 producthunt 这样的无限滚动页面。

下面是您可以在浏览器控制台上运行的完整代码。确保取消注释运行`hundredProducts()`的行，然后记录`products`数组。

```
const delay = d => new Promise(r => setTimeout(r, d));

const scrollAndExtract = async (selector, leaf, remove) => {
  const element = document.querySelector(selector);
  if (element) {
    element.scrollIntoView();
    if (remove) return element.remove(); // <-- Remove and exit
    return element[leaf];
  }
};

async function extractor() {
  const title = await scrollAndExtract(".title_9ddaf", "innerText");
  const img = await scrollAndExtract('[data-test="post-thumbnail"] img', "src");

  // remove the parent here
  await scrollAndExtract("div.white_09016 ul li", null, true);
  return { title, img };
}

const products = [];
async function hundredProducts() {
  if (products.length < 100) {
    await delay(500);
    window.scrollTo(0, 0);

    const data = await extractor();
    if (!data.title || !data.img) return null;

    products.push(data);
    return hundredProducts();
  }
}

// hundredProducts().then(() => console.log(products)) 
```

Enter fullscreen mode Exit fullscreen mode

## 可选:木偶剧本

如果你想用 puppeteer 自动完成，你可以把代码放在一个`page.evaluate`函数中。这里是一个片段，这里是有完整代码的 [**git repo**](https://github.com/entrptaher/plaground-scrape-infinity-load) 。

```
const puppeteer = require("puppeteer");

(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto("https://www.producthunt.com");
  const productList = await page.evaluate(async () => {
    // paste the final code here
    // ...
    // run the function to grab data
    await hundredProducts();
    // and return the product from inside the page
    return products;
  });

  await browser.close();
})(); 
```

Enter fullscreen mode Exit fullscreen mode

## 关闭思绪

这看起来比互联网上所有其他帖子大十倍。但是你知道上面的原始版本也要小得多。没有疯狂的滚动高度或其他。

但希望我能向你展示一种不同于你通常刮的方式。请随意摆弄和试验这些数据。

让我知道你对这种方法的看法，以及你认为一般来说抓取无限滚动页面的最好方法。