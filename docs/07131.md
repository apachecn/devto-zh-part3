# 将 blog-pwa 从聚合物更新为 LitElement、Workbox 和 Rollup

> 原文：<https://dev.to/justinribeiro/updating-blog-pwa-from-polymer-to-litelement-workbox-and-rollup-2joj>

在上个月写了一篇文章并运行了一个构建之后，我注意到一个令人不安的事实，我的基本 web 组件`blog-pwa`大得惊人，有 71.9K 的 gzipped。这似乎是不对的；我并没有从本质上改变我使用多年的 my core [blog-pwa](https://github.com/justinribeiro/blog-pwa) 项目的任何主要基础。快速浏览了一下我在聚合 2 到聚合 3 的转换过程中留下的构建和一些随机的待办事项，我很快意识到我已经把一些改变推到了次要位置。

现在不是修复这些东西的时候了，是时候开始做一些全面的大的有益的改变了。我定下心来:

1.  将 web 组件从聚合物移动到列表元素
2.  从使用 polymer-cli 转向构建汇总
3.  从使用 polymer 的 sw-toolbox 代转移到 workbox
4.  动态进口，多填充或破裂。
5.  放弃那些依赖，减少负重。
6.  在分析中开始跟踪 FP，FMP，FID。

所有这些，而且要快。骑着摩托 G4。在 3G 上。看起来很简单，对吗？让我们言归正传。

## 从聚合物走向元素

毫无疑问:我仍然非常喜欢 Polymer 3 和工具。我在许多其他项目中使用它，你可以快速地构建它。我的问题甚至不是真的与 Polymer 3 有关，而是我携带了导致我的构建膨胀的遗留 Polymer 依赖的事实(我在看你的棱镜元素)。

也就是说，来自聚合物团队的 [LitElement](https://lit-element.polymer-project.org/) 是未来，这是有充分理由的。它很小，吸收了聚合物的一些优点，而且速度很快。采取这一举措需要重新思考 blog-pwa 应该如何运作:

1.  我想要同样的动态路径加载来保持我的应用程序外壳和基础包中的 PRPL 模式和沟渠权重，但是我不希望`app-route`这样做。
2.  我想抛弃我用来做任何渲染数据工作的任何观察器。
3.  我需要重做 CSS 来利用 LitElements 使用的[可构造样式表](https://developers.google.com/web/updates/2019/02/constructable-stylesheets)。

首先，我去掉了最初的 app shell 组件，并转换为 extend LitElement。这不是特别大的问题，因为 shell 没有承载太多的重量，并且延迟加载了渲染内部视图可能需要的其他部分。更大的问题是如何处理路由。我想要有足够功能的轻便的东西，这样我就不用做太厚的垫片了。进入[vaa din-router](https://github.com/vaadin/vaadin-router)；

Vaadin-router 仅重 7.8K gzipped，与框架无关，并且不关心如何创建 web 组件。在我的例子中，我决定将它作为我的应用程序外壳的一部分，并在 DOM 通过 LitElements `firstUpdated()`生命周期方法第一次更新后进行实例化。

从那以后，只需要将我的 routes 转换成 express.js 风格的语法，并使用`action()`方法来定义我的动态导入:

```
const outlet = this.shadowRoot.querySelector('main'); const router = new Router(outlet, null); router.setRoutes([{ path: '/', children: [ { path: '', component: 'blog-static', action: () =\> { \_\_import('blog-static.js').then(() =\> { const check = this.shadowRoot.querySelector('blog-static'); check.mount('index'); }); } }, //... more routes ... 
```

你可能想知道`__import()`方法是怎么回事。我们一会儿会谈到这一点。

从那以后，它实际上是关于如何定义那些内部视图组件实际做什么。如您所见，我们的`<blog-static>`元素有一个名为`mount()`的公共方法，允许我们告诉它我们希望它在数据加载方面做什么，并做一些额外的内务处理。

对于这些内部视图，我决定编写一个名为`BlogElement`的新基类，它将扩展 LitElement 并处理我们共享的 CSS 和一些我用来检查页面元数据的方法，以便用户获得良好的体验。共享的 CSS 对于扩展 BlogElement 的组件来说很关键，因为这允许我们使用`super.styles`来保持我们经常重用的组件的最佳性能:

```
static get styles() { return [super.styles, css` #shoutout p { font-size: 28px; } /* ... other styles ... */ `]; } 
```

随着样式和装裱的分类，去除 blog-pwa 使用的有限数量的观察者是没有痛苦的。随着我们的主要元素的转换，是时候弄清楚如何让它与不支持动态导入的东西一起工作了。

## 动态导入并在哪里找到它们

我完全可以跳过这个。我*可能*已经写了一个多构建输出。我*本可以*改变 App Engine 的服务方式。真的，那有什么好玩的？

动态导入在 Chrome 和 Safari 中已经有一段时间了，但 Firefox 仍然没有(尽管将在 5 月中旬推出)，Edge 也没有(尽管 Chrome 的整体迁移很快将使这一点变得没有意义)。无论如何，我不想混淆它，我想检查加载聚合填充或垫片。

经过大量的各种测试，我选定了一个经过修改的版本 [uupaa 的 importModule.js](https://github.com/uupaa/dynamic-import-polyfill/blob/master/importModule.js) ，只有在浏览器不支持动态导入的情况下我才会加载这个版本:

```
// Only load the dynamic import polyfill if we need it function \_\_loadDynamicImportCheck(src) { window.polyfillDynamicImport = false; try { new Function('import("./' + src + '")')(); } catch (e) { var s = document.createElement('script'); s.src = '/src/polyfill-dynamicimport.js'; s.dataset.main = src; document.head.appendChild(s); window.polyfillDynamicImport = true; s.onload = () =\> { \_\_import(src); } } } \_\_loadDynamicImportCheck('blog-pwa.js'); 
```

一旦你有了它，我们就写一个小的全局函数，在我们的组件中使用，或者在任何需要动态导入的地方使用:

```
function \_\_import(src) { if (window.polyfillDynamicImport) { return importModule(`./src/${src}`); } else { return new Function('return import("./' + src + '")')(); } } 
```

你可能想知道为什么我使用`new Function()`而不是仅仅返回导入。这是专门用来处理 Firefox with 在解析过程中遇到动态导入时抛出错误，导致我们脚本的其余部分失败(以及我们的 PWA)的情况。

现在，我还没有对这个解决方案进行足够多的测试来说“您应该在任何地方都这样做！”。如果我没有指出，如果你像我一样使用内容安全策略(CSP)，那么即使你使用 SHA 散列或 nonce，因为它需要`unsafe-eval`，所以`new Function()`尤其成问题。您的里程数将有所不同，但它的工作没问题，因为我想使用它，直到我们有更多的动态导入支持。

## 东西加载，东西渲染，让我们用 rollup 构建一些东西

随着组件的加载和渲染，我们需要将它们构建成我们可以发布的东西。很长一段时间以来，我一直使用 polymer-cli 来做这件事，但由于 mjs 文件的一些问题，我决定是时候去掉中间部分，直接转到 [rollup.js](https://rollupjs.org/) 。

我对使用 rollup 并不陌生，但是在我的情况下，由于我的动态导入垫片，这将是一种稍微不同的东西。我决定做的只是将所有组件输入到输入中，并输出一个包含各种包和组件的目录。如果你来自于`polymer.json`，你可以这样想，把你的`fragments`数组和你的`entrypoint` :
组合起来

```
// rollup.config.js import resolve from 'rollup-plugin-node-resolve'; import minify from 'rollup-plugin-babel-minify'; export default { input: ['src/app.js', 'src/blog-pwa.js', 'src/blog-static.js', 'src/blog-entry.js', 'src/blog-chronicle.js', 'src/lazy-resources.js', 'src/3d-utils.js', 'src/pre.js'], output: { dir: 'build/default/src', format: 'es', sourcemap: true }, plugins: [resolve(), minify({ mangle: { exclude: { "\_\_import": true } }, comments: false, plugins: ['transform-remove-console'] }) ] }; 
```

你可以看到的另一件事是我们在这个构建过程中使用 babel-minify 插件来处理到一个更小的文件的转换。这与 polymer-cli(使用 babel 和 rollup 无缝地处理这类事情)没有什么不同，尽管我们没有 babel 助手或编译目标。

rollup 没有做的一件事是处理我们构建可能需要的其他资源的混合，比如我们的`index.html`或我们的图像或 JSON 数据文件。Polymer-cli 为我们做了这个，但是因为我们在这里没有使用它，我们编写了一些命令，在我们的`package.json` :
中进行复制和移动

```
// package.json "scripts": { "build:clean": "rm -rf build && mkdir -p build/default/src && mkdir build/default/node\_modules", "build:copyDeps": "cp -r node\_modules/@webcomponents/ build/default/node\_modules && cp -r node\_modules/intersection-observer/ build/default/node\_modules && cp -r node\_modules/prismjs/ build/default/node\_modules && cp -r src/polyfill-dynamicimport.js build/default/src", "build:copyRoot": "cp index.html build/default && cp robots.txt build/default && cp manifest.json build/default && cp push\_manifest.json build/default", "build:copyData": "cp -r images/ build/default && cp -r data/ build/default && cp -r helpers/ build/default", "build:minify": "yarn html-minifier --collapse-whitespace --remove-comments --remove-optional-tags --remove-redundant-attributes --removeAttributeQuotes --remove-tag-whitespace --removeOptionalTags --minify-css true --output build/default/index.html build/default/index.html", "build:modules": "rollup -c", "build:dist": "yarn build:clean && yarn build:copyDeps && yarn build:copyRoot && yarn build:copyData && yarn build:minify && yarn build:modules" }, // ... 
```

## 我们有了一个构建，现在让我们确保我们的服务人员正在运行

工具箱，让服务人员轻松工作的无名英雄。让 workbox 与构建一起工作实际上只是将旧的 sw-precache 配置转换成新的 workbox 配置格式的问题。这些都在 sw-* tools 的[迁移文档中有很好的记录，所以我不会在这里重写这些文档。](https://developers.google.com/web/tools/workbox/guides/migrations/migrate-from-sw)

也就是说，我确实决定重写我的运行时缓存策略，使之更加工作箱友好，用更好的模式替换我以前使用的一些相当具体的路径。我还决定使用[工具箱窗口](https://developers.google.com/web/tools/workbox/modules/workbox-window)，它在`<blog-pwa>`内仅重 2K gzipped 来处理我的服务人员
的安装和更新

```
// blog-pwa.js / \_ensureLazyLoaded() method if ('serviceWorker' in navigator) { const wb = new Workbox('/service-worker.js'); wb.addEventListener('activated', (event) =\> { if (!event.isUpdate) { this.\_setSnackBarText('Ready to work offline.'); } // Get the current page URL + all resources the page loaded. const urlsToCache = [location.href, ...performance.getEntriesByType('resource').map((r) =\> r.name),]; // Send that list of URLs to your router in the service worker. wb.messageSW({ type: 'CACHE\_URLS', payload: {urlsToCache}, }); }); wb.addEventListener('waiting', (event) =\> { this.\_setSnackBarText( 'New and updated content is available.', 0, true, async () =\> { wb.addEventListener('controlling', (event) =\> { window.location.reload(); }); wb.messageSW({type: 'SKIP\_WAITING'}); }); }); wb.register(); } 
```

你可以看到我实际上使用了工具箱文档中的[高级配方](https://developers.google.com/web/tools/workbox/guides/advanced-recipes)的组合。第一，在初始安装和激活时，我通过 workbox-window 提供的`messageSW()`将现有的页面资源存入缓存。第二，我没有在我的工具箱配置中使用`skipWaiting`,所以我可以利用工具箱在构建时注入的`SKIP_WAITING`钩子。

## 通过谷歌分析测量真实用户表现

坚实的构建现在运行良好，是时候确保我可以获得一些额外的性能数据，这样我就可以了解实际的用户性能了。为此，我编写了一个小方法来将一些相关数据拉入 Google Analytics:

```
// blog-pwa.js \_\_sendPerfData() { perfMetrics.onFirstInputDelay((delay, evt) =\> { ga('send', 'event', { eventCategory: 'Perf Metrics', eventAction: 'first-input-delay', eventLabel: evt.type, eventValue: Math.round(delay), nonInteraction: true, }); }); performance.getEntriesByType('paint').forEach((entry) =\> { ga('send', 'event', { eventCategory: 'Perf Metrics', eventAction: entry.name, eventLabel: entry.entryType, eventValue: Math.round(entry.startTime), nonInteraction: true, }); }); } 
```

在这种情况下，我已经注入了 [FirstInputDelay 库](https://github.com/GoogleChromeLabs/first-input-delay)，它的重量大约为 400 bytes gzipped，来处理这个计算。其他指标，比如第一次绘制和第一次内容丰富的绘制来自[性能时间线 API](https://developer.mozilla.org/en-US/docs/Web/API/Performance_Timeline) 。

最后，我将它包装在一个`requestIdleCallback()`中，以确保我们没有阻塞任何重要的东西。我还设置了一个超时，确保它确实运行:

```
// blog-pwa.js / \_ensureLazyLoaded() method if ('requestIdleCallback' in window) { requestIdleCallback(this.\_\_sendPerfData, { timeout: 2000 }); } else { // booo this.\_\_sendPerfData(); } 
```

## 所有这些都有效，但是它更小更快吗

如果我只是宣布这已经完成，手头的任务已经完成，那将是我的失职。显然，我们必须追溯和比较我们的起点和结果。

从规模的角度来看，我们看到了立竿见影的效果:

```
---------------------- Old New blog-pwa shell size: 71.9KB 19.9KB 
```

随着所有惰性加载资产的完成，整个基本加载的大小也有所减少:

```
-------------------------- Old New page size w/LL components: 86.6KB 32.7KB 
```

坚实的改进，节省字节。让我们来看看 3G 版 Moto G4 的一些网页测试数据:

```
------------------------------- Old New First View - start render: 1.690s 1.279s First View - speed index: 2.067s 1.730s First View - first interactive: 2.926s 2.491s First View - RUM First Paint: 1.542s 1.154s 
```

再次，坚实的改进，将近半秒。

检查我们的灯塔指标:

```
------------------------------- Old New First Contentful Paint: 0.8s 0.6s Speed Index: 2.0s 1.7s Time to Interactive: 2.1s 0.7s First Meaningful Paint: 0.8s 0.6s First CPU Idle: 2.1s 0.7s 
```

最大的改进是 TTI 和 CPU 空闲，这是我们希望在那些受限的设备上实现的。

如果你想看到所有的数据，请查看[网页测试结果](https://www.webpagetest.org/result/190411_T6_d96d381edba4bfb2726642074cb7e136/1/details/)以获得更深入的了解。

## 总结思想

总而言之，我觉得搬到 LitElement 相当直接。请注意，在我回到 blog-pwa 之前，我已经在其他一些地方这样做过了，但是在我看来，理解您希望您的组件做什么是最重要的。在选择依赖项时要严格，要始终如一地度量和跟踪，并进行删减。

blog-pwa 最初是一项实验，将先进的 web 应用程序技术应用于新的 web 平台功能，如 web 组件和服务工作者。考虑到它为这个网站提供了动力，有人会认为我不会在上述性能数字的基础上进一步突破界限。

但是这有什么意思呢？谁从停滞中受益？谁也不是，就是谁。

以后我会继续用 blog-pwa 探索。我会不断完善这些数字，总的来说，我会享受破坏和修理东西的乐趣。我们继续前进！