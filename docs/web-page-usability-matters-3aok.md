# 网页可用性很重要

> 原文：<https://dev.to/addyosmani/web-page-usability-matters-3aok>

用户喜欢页面在视觉上做好准备后不久就可以使用和交互。UI 交互(滚动、点击、点击)会被脚本和其他浏览器工作延迟，所以最小化它们的影响可以真正帮助你的用户。

你可能听说过没有一个单一的指标可以完全捕捉到一个网页的“加载体验”。加载页面是一个渐进的过程，有四个关键时刻:它正在发生吗？有用吗？有用吗？这令人愉快吗？。

[![When did the user feel they could interact? When could they interact? Speed metrics illustrate First Paint, First Contentful Paint, Time to Interactive for a page](img/92f2e830f006b379e2b9c778ad0bbb45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i-7vdEAn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9enrd4e7p4slrlgns6t1.png)

就可测量的指标而言，这些时刻细分如下:

*   这是真的吗？:导航是否已经成功启动？服务器开始响应了吗？度量:[先画](https://www.w3.org/TR/paint-timing/)
*   **有用吗？**:当你绘制了文本、图像或内容，让用户从体验中获得价值并参与其中。指标:[第一个有内容的油漆](https://developers.google.com/web/tools/lighthouse/audits/first-contentful-paint)，[第一个有意义的油漆](https://developers.google.com/web/tools/lighthouse/audits/first-meaningful-paint)，[速度指标](https://sites.google.com/a/webpagetest.org/docs/using-webpagetest/metrics/speed-index)(实验室)
*   是否可用？:当用户可以开始与体验进行有意义的交互并有事情发生时(例如，点击一个按钮)。这一点非常关键，因为如果用户试图使用看起来已经准备好但实际上还没有准备好的 UI，他们可能会感到失望。指标:[交互时间](https://developers.google.com/web/tools/lighthouse/audits/time-to-interactive)(实验室)、[第一 CPU 空闲](https://developers.google.com/web/tools/lighthouse/audits/first-cpu-idle)、[第一输入延迟](https://developers.google.com/web/updates/2018/05/first-input-delay)(字段)
*   令人愉快吗？:愉悦是指确保用户体验的性能在页面加载后保持一致。没有 janking 能流畅滚动吗？动画流畅并以 60fps 运行吗？其他[长任务](https://calendar.perfplanet.com/2017/tracking-cpu-with-long-tasks-api/)会阻止这些发生吗？。

[![Time to Interactive as highlighted by Chrome DevTools and Lighthouse. The user sees content early on but the page is not interactive until much later.](img/5f927301e3cf099c9e6125a936a0795d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cxCDnotU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2g63ozqm2x3wyce97c8a.jpg)

页面可用性很重要。如果单页应用程序没有完成加载附加事件处理程序所需的 JavaScript，或者占用了浏览器的主线程(与上面类似)，它们就不太可能可用(能够快速响应用户输入)。这就是为什么监控可用性度量很重要的原因。

## 用户关心页面可用性吗？

2018 年，Akamai 利用[脉冲](https://www.akamai.com/us/en/products/web-performance/mpulse-real-user-monitoring.jsp)进行了一项 [UX 研究](https://speakerdeck.com/bluesmoon/ux-and-performance-metrics-that-matter-a062d37f-e6c7-4b8a-8399-472ec76bb75e)互动对[愤怒点击](https://blog.fullstory.com/rage-clicks-turn-analytics-into-actionable-insights/)的影响。当用户因沮丧而快速点击(或轻敲)网站时，就会出现疯狂点击。Akamai 发现**愤怒点击的可能性取决于页面可用性的延迟**:

*   如果用户的第一次交互是在页面变得可交互之前(在交互之前或`onload`)，那么 Rage 点击是一致的。这可能是因为事件处理程序占用了 CPU。
*   在 30%以上的情况下，页面在`onload`触发后是交互的，在 15%的情况下，用户在`onload`和交互之间的某个时间尝试交互。

[![Rage Clicks by first interaction and visually ready. There is a clear correlation.](img/c576597e4b9bd2cee17ad9c22dafa46f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9vFe7sR---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6ok1usi72wmur9ssgqjy.jpg)

减少狂怒点击的最佳时间是什么时候？

[![What is the optimum time to reduce Rage Clicking? Pages can be visually complete. Users expect they can interact soon after (1.3x). There is the potential for a rage-click soon after.](img/e28bdf9fe05e56fe203ebe88896dde96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2imjptqM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vxcakzxga0zm55znq8rt.jpg)

Akamai 观察到:

*   大多数疯狂点击的用户试图与一个页面进行交互的时间是视觉准备时间的 1.25 到 1.5 倍。
*   他们建议确保你的页面是交互式的，并在视觉准备时间的 1.3 倍之前加载。

有关这项研究的更多信息，请参见 Philip Tellis 的 [UX 和重要的绩效指标](https://speakerdeck.com/bluesmoon/ux-and-performance-metrics-that-matter-a062d37f-e6c7-4b8a-8399-472ec76bb75e)。

**互动时间可能与总体转换率高度相关**

在 2017 年的一项研究中，Akamai 和 Chrome 发现，在三个现实世界的网站(零售、旅游和游戏)中，互动时间与整体转化率有很高的相关性。转换可以是点击一个按钮来完成购买流程，或者是对交互的任何其他类型的响应。

[![Time to Interactive impact on conversion rates](img/88e0021f0775a1c73054314b1a412a91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ze16kW3J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gy6g572iop59oigb86m8.png)

他们发现:

*   漫长的任务直接拖延了互动的时间。
*   随着首页长任务时间的增加，整体转化率下降。
*   移动设备的任务时间可能是台式机的 12 倍。
*   旧设备可能会将一半的加载时间花在长时间的任务上。

注意:这显然是一个很小的样本量，每个站点都可能不同。

## 更详细的加载阶段

### 发生了吗？有用吗？

当用户及时得到“正在发生”的反馈时，他们会感觉更好，他们会觉得网站运行得更快了。同时，你不希望用户呈现一个“有用”的页面，但是他们不能与之交互，因为它还没有准备好。这会让他们觉得它不可用。

[![Perceptual load timings. The timestamp of a shipped frame that contains any of these - first paint of text, first paint of SVG](img/947515a5263359ec0c47ecf7fdb14901.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Op1KORLs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9vhm0ypfgg1g5qobo7x5.jpg)

#### 先作画

当浏览器可以呈现任何视觉上不同于导航前显示的内容时，首先绘制标记。它确认渲染已经开始。这可能是一个重要的指标，因为“黑屏”的持续时间可能是页面放弃的最重要指标。

#### 先心满意足的作画

第一次内容丰富的绘制是在浏览器呈现来自 DOM 的第一个内容时——这可能是文章文本、图像或 SVG。希望这是一次成功的绘画交流导航开始了。

### 是否可用？

正如我在[中提到的 JavaScript](https://medium.com/@addyosmani/the-cost-of-javascript-in-2018-7d8950fbb5d4) 的成本，网络有一个问题。许多网站都在优化内容的可见性，但忽略了交互性，因为实现这一点的 JavaScript 需要时间来处理。这意味着大量非常受欢迎的网站在绘制有用的东西和“可用”或交互之间有[多秒钟的](https://httparchive.org/reports/loading-speed#ttci)延迟。在此期间，网络感觉缓慢且无响应。

#### 到交互和首次输入的延迟时间

[![First Contentful Paint - FCP - when the main thread is idle, styles are loaded and browser can paint content. First Interactive - when the browser can respond to the first user input](img/1ee4edeab4654fc6051def56999addbd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hH_NChbG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1h5f9t29ggc0mh5v2vqy.png)

[互动时间](https://github.com/WICG/time-to-interactive) (TTI)是一个衡量网页变得互动所需时间的指标。这被定义为一个点，当:

*   该页面显示了有用的内容
*   为大多数可见的页面元素注册了事件处理程序
*   当用户与页面交互时，页面始终在 50 毫秒内做出响应——用户不会遇到 jank。

当一个页面有一个很好的 TTI 时，用户可以在界面上轻敲，并确信它会响应输入。这严格符合 [RAIL 性能模型](https://developers.google.com/web/fundamentals/performance/rail)的空闲准则:页面至少每 50 毫秒将控制权交还给主线程一次。网络空闲。具体来说，只剩下两个打开的网络请求。

首次输入延迟(FID)是 TTI 在该领域的补充指标——它测量从用户首次与页面交互(如点击按钮)到浏览器实际响应交互的时间。

## 优化以用户为中心的指标

专注于优化以用户为中心的指标将最终改善用户体验。如果你想减少...

互动或首次输入延迟时间:

*   少做工作
*   使用[代码分割](https://web.dev/fast/reduce-javascript-payloads-with-code-splitting)分割大型 JavaScript 包
*   拆分长时间的任务。考虑将密集的非主线程转移到工作线程。
*   将非关键工作推迟到页面加载之后

第一幅画和第一幅内容丰富的画:

*   从 head 中删除渲染阻塞脚本
*   确定所需的[关键 CSS](https://www.smashingmagazine.com/2015/08/understanding-critical-css/) 并嵌入`<head>`
*   App 外壳模式——提升用户感知渲染 UI 骨架

## 监控指标

像 [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/) 、 [WPT](https://webpagetest.org/easy) 和 [Lighthouse](https://developers.google.com/web/tools/lighthouse/) 这样的性能工具捕捉以用户为中心的负载指标:

[![First Contentful Paint, Speed Index, Time to Interactive, First Meaningful Paint, First CPU Idle in Lighthouse](img/7a83eb1b5943bfbeec707c1e3c16667b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zUAzOJSF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mt5uxwewjkgy3bntc8av.png)

对于 CLI 用户，也可以通过 Paul Irish 和 Artem Denysov 的 [pwmetrics](https://github.com/paulirish/pwmetrics) 获得。

对于现场监控指标(通过 RUM)，我推荐使用[画图计时 API](https://developer.mozilla.org/en-US/docs/Web/API/PerformancePaintTiming) ，它提供了第一次画图和第一次内容画图。还提供了用于[第一输入延迟](https://github.com/GoogleChromeLabs/first-input-delay)的多填充物。配合分析服务，这些可以为你的真实用户记录渐进式网络指标。

[Chrome 用户体验报告](https://developers.google.com/web/tools/chrome-user-experience-report/) ( [也在 PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/?url=https%3A%2F%2Fchrome.com) 中)为使用 Chrome 的真实用户提供了对这些指标的访问(如首次内容丰富的绘画和首次输入延迟):

[![Chrome User Experience Report distributions for metrics](img/a7cb76403734a4465d4eee72fa62db85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eZpbjtv_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2zyvauug3fdk6kqudgi6.png)

我还喜欢用 [SpeedCurve](https://speedcurve) 或 [Calibre](https://calibreapp.com) 来跟踪 FCP 和 TTI 等指标。它还允许我为他们设置性能预算，这有助于发现退步:

[![Speedcurve metrics tracking](img/705c1fda455e8b27d567420db1565767.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cjrksdQA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4mw8beesk2j3lc3qna4e.png)

## 调试指标

Chrome DevTools 现在在性能面板中突出显示性能指标。这些可以在计时下找到:

[![DevTools Metrics in performance panel](img/1ef036985c8b5e9bee06131e7e80cb19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lbblscnX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hb2e04oc9ej4h8b4gqhn.png)

除了暴露这些度量的其他表面之外，这可以在尝试改进迭代工作流中的时间时提供价值。

## 参考资料，了解更多

*   [最新的指标&测量——保罗·爱尔兰](https://www.youtube.com/watch?v=XvZ7-Uh0R4Q)
*   [UX 和重要的绩效指标——菲利普·泰利斯](https://speakerdeck.com/bluesmoon/ux-and-performance-metrics-that-matter-a062d37f-e6c7-4b8a-8399-472ec76bb75e)
*   [以用户为中心的绩效指标——Philip Walton](https://developers.google.com/web/fundamentals/performance/user-centric-performance-metrics)
*   [可靠地测量野生动物的反应——Shu bhie Panicker 和 Nick Jansma](https://www.slideshare.net/nicjansma/reliably-measuring-responsiveness)
*   [速度工具——国情咨文:伊丽莎白·斯维尼和保罗·爱尔兰](https://www.youtube.com/watch?v=ymxs8OSXiUA)
*   [关注以人为中心的指标- Radimir Bitsov](https://calibreapp.com/blog/time-to-interactive/)
*   [使用渐进式网络指标改进您的网络应用——Artem Denysov](https://www.slideshare.net/fwdays/improve-your-web-application-using-progressive-web-metrics)
*   [为什么 Web 开发者需要关注交互性——Philip Walton](https://philipwalton.com/articles/why-web-developers-need-to-care-about-interactivity/)
*   [第一输入延迟-菲利普·沃尔顿](https://developers.google.com/web/updates/2018/05/first-input-delay)
*   [第一次输入延迟-卷筒纸状态-瑞克粘度计](https://www.youtube.com/watch?v=ULU-4-ApcjM)
*   [在线理解挫折指南](https://www.fullstory.com/resources/guide-to-understanding-frustrating-user-experiences-online/)
*   [第一次输入延迟:与 TTI -蒂姆·德莱赛的相关性](https://docs.google.com/document/d/1g0J5JIcICFyXYM6ifBF6vNd_qUPIfpf6YAfFHE1TYRE/edit#heading=h.c79uz11ezek4)
*   [一个事件计时 API - Tim Dresser 和 nicolás pea Moreno](https://wicg.github.io/event-timing/)