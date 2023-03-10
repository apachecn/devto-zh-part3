# 图像优化:了解对谷歌的性能得分排名更高的确切影响

> 原文：<https://dev.to/rsedykh/image-optimization-understanding-the-exact-impact-on-google-s-performance-score-to-rank-higher-24aa>

如今，每个人都在谈论 Web 性能。网站速度是一个排名因素，并影响商业指标:T2、反弹访客和 T4 的页面浏览量。然而，通常不清楚性能的提高如何确切地影响这些指标。

许多关于网页性能的文章提到“图像优化”是加速网站的首要步骤。现在，确切的影响是什么？这篇文章详细阐述了**图像优化如何影响谷歌的性能得分**。

> 性能分数是通过 Lighthouse 模拟页面负载并分析得出的实验室数据来确定的。100 是最好的分数，代表第 98 个百分位数，是表现最好的网站。50 分代表第 75 百分位。
> 
> 90-100 分被认为是快，50-90 分被认为是一般。低于 50 被认为是慢的。

我们选择了世界 500 强网站(如今日美国、宜家和 Behance)并用[灯塔](https://developers.google.com/web/tools/lighthouse/) *(谷歌制造的性能分析器)*和 [PageDetox](https://pagedetox.com/?utm_source=devto&utm_content=performancescore) *(美国制造的诊断框架)*进行性能分析。

我们想分享这些数字，这样你可以更好地了解你的立场。

### 关于优化 web 性能的炒作值得吗？

你为什么要在乎？答案很简单——竞争。

你可以用很多方法来优化你的网站。拿搜索引擎优化来说；当用正确的关键词、内容长度进行优化，并有权威域名支持时，你的网站在搜索结果中的排名会更高。

然而，虽然平均灯塔搜索引擎优化分数是 93 分，但是性能分数只有 48 分，T2 是 48 分。

这对你意味着什么？你可以选择在哪里进行优化。简单地说，在搜索引擎优化上超过平均水平意味着达到 **93+** 分，同时在性能优化上有大约两倍的机会。此外，当你可以快速达到目标性能指标时，需要时间来证明你的 SEO 策略。

如果你的利基是有竞争力的，并且一个搜索引擎正在对两个内容和搜索引擎优化得分相似的网站进行排名，它将优先考虑性能得分较高的网站。

从用户体验的角度来看，这也是合乎逻辑的。你的站点越快，用户体验越流畅；因此，你的分析平台记录了更少的反弹，更多的页面浏览量和更高的转化率。

在我们的研究中，我们关注的是被分析页面的总图片权重。平均来说，图片占你整个页面重量的 53% (根据 [Pingdom](https://royal.pingdom.com/page-load-time-really-affect-bounce-rate/) )。这意味着它们对页面加载时间以及随后的整体性能有很大影响。

### 有点理论

本节揭示了图像性能如何影响性能得分。这有点技术性，所以如果你想要 500 强网站的实际数字，直接跳到下一部分。

Lighthouse 使用以下指标根据权重计算绩效得分:

*   第一次内容丰富的绘画 *(FCP，用户感知一个页面作为响应)*，权重 3
*   第一个有意义的颜料 *(FMP，页面的主要内容出现在屏幕上)*，权重 1
*   速度指数 *(SI，显示可见页面内容)*，权重 4
*   第一个 CPU 空闲*(页面变成最小交互)*，权重 2
*   互动时间 *(TTI，页面完全互动)*，权重 5

虽然以上都不是“图像内容权重和加载时间”，但您可以在 Lighthouse 报告中找到特定于图像的性能改进机会。它们是:

*   延迟屏幕外图像，实现延迟加载。
*   适当调整图像大小，实现响应性图像。
*   高效编码图像，平衡质量水平。
*   提供下一代格式的图像，尽可能使用 webp。
*   将视频格式用于动画内容，将 gif 转换为视频格式。

注意，这些可以在你的完整的 Lighthouse 报告 JSON 中的“load-opportunities”键下看到，权重为零。这意味着这些图像问题不会直接影响您的绩效得分计算。但是，他们确实利用了绩效得分的所有五个组成部分。

因此，我们的想法是，通过优化图像及其交付，我们可以改善所有五个绩效得分组成部分。现在，让我们继续我们的发现。

### 以下是我们的发现

在分析了 500 多家领先网站的主页后，我们得出了以下平均值:

*   图像计数:桌面上 **43** ，手机上 **23** 。
*   页面重量:桌面版 **3.64** MB，移动版 **2.69** MB。
*   图像内容权重: **1.40** / **0.74** MB，这样就得到每张图像 33 / **32** KB。
*   页面加载时间:宽带(10Mb/s)下 **7.6** 秒，慢速 4G (1.5 Mb/s)下 **13.92** 秒。
*   Lighthouse 性能得分(标准化为 0-1 分):桌面端 **64** ，移动端 **48** 。

[![](img/b776a1db8b0b6babedd07ee81cc4bfb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tUZJfIHW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ucarecdn.com/c0a57cff-82f0-428e-b6ac-905c2033fe75/average_numbers.png)

有趣的是，在我们榜单上表现最好的网站中，有明显的候选网站，如 google.cn和 wikipedia.org，还有不太明显的，如 wordreference.com 和 ikea.com 的[。在“呆滞”的一端是](http://wordreference.com)[usatoday.com](http://usatoday.com/)、[kaskus.co.id](http://kaskus.co.id/)和[deviantart.com](http://deviantart.com)。

[*这里的*](https://docs.google.com/spreadsheets/d/1K1qUIPCm2ZOxwCW52Zu5oLo5p9r8xydEh5h97HF3eyU/edit?usp=sharing) *是我们收集的原始数据，用来分析世界 500 强网站的表现。我们突出显示了我们无法收集数据的网站行:两个分析器中的一个失败，等等。*

那么，改进的空间在哪里呢？

让我们来看看灯塔报告对**桌面** / **移动**网站的见解:

*   59 % **/ 62** %需要改进图像格式。
*   **57** % **/ 36** %的响应图像有问题。
*   **34** % **/ 32** %有图像权重问题。

[![](img/a37c2af65fc971789222a8bb44334e12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ia80NuwB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ucarecdn.com/e30dfda5-9bd7-4032-802b-6ad281c2c140/require_improvements.png)

### 能达到什么目的

如果我们解决了所有这三类问题，平均来说，我们将:

*   在桌面上减少图片内容权重 62%在移动设备上减少 T2 66%在移动设备上。
*   分别减少页面加载时间 **8** % / **20** %。
*   减少被反弹访客的数量 **19** % / **9** %。
*   将用户在单次会话中浏览的网页数量增加 **13** % / **14** %。

[![](img/5e175cb71820b1c4dd9bdad22159a8f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GWsQQw_f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ucarecdn.com/aaca02ba-1d95-4d65-b1f3-c795cffdf3ef/average_savings.png)

### 实际表现得分呢？

我们可以预测影响。

由于谷歌提供了其[性能分数计算器](https://docs.google.com/spreadsheets/d/1Cxzhy5ecqJCucdf1M0iOzM8mIxNc7mmx107o5nj38Eo/edit#gid=283330180)，剩下的唯一事情就是了解新的 FCP、FMP、SI、CPU 和 TTI 将会是什么。

多亏了 Lighthouse，我们知道了被分析站点的参数值。性能分数的所有五个组成部分都以秒表示。

当前状态:

*   第一幅令人满意的画— **1.68** / **4.05**
*   第一次有意义的绘画— **1.78** / **4.38**
*   速度指数— **2.90** / **7.54**
*   第一次 CPU 空闲— **2.62** / **7.67**
*   互动时间— **2.92** / **10.10**

我们还知道:

1.  通过优化图像节省的字节数
2.  每个桌面和移动主页版本的初始加载时间
3.  我们可以从加载时间中减去的秒数
4.  装载时间改进的百分比

我们假设，如果我们按比例减少这些指标，我们可能会得到一个可靠的近似值。

预测结果:

*   第一幅令人满意的画— **1.51** / **3.52**
*   第一次有意义的绘画— **1.60** / **3.82**
*   速度指数— **2.64** / **6.68**
*   第一次 CPU 空闲— **2.39** / **6.93**
*   互动时间— **2.67** / **9.11**

[![](img/4d6aae62d6d1641c00e0cfcf23594fd0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zNdeN84P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ucarecdn.com/8cf075f3-7bcc-42a9-bc6d-aa406e1e751c/performancemetrics.png)

**鉴于此，全球 500 强网站的平均优化性能分数在桌面和移动方面将分别提高 4 分和 7 分，表现最差的 10%网站的平均优化性能分数将高达 25 / 33。**

[![](img/3124179ff8a1fc26b7acc37525b89c8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rHAV6Zra--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ucarecdn.com/38c4b09e-e742-4bbc-8412-053a4694281e/performancescoreforecast.png)

我们不能确切地说出改进的性能分数将如何影响被分析网站的 SERP 排名(没人能，包括谷歌)。事实是，最大化分数增加了获得更高排名的机会。

在加速站点的同时，我们也在一路提升用户体验。然后，我们可以预计，减少反弹访问者的数量和增加页面浏览量会直接影响被分析网站漏斗顶端的转化率。

图像优化是实现这一目标的最简单快捷的方法之一。而且，即使是世界 500 强网站也有改进的空间，所以要走在前面。

这是唾手可得的果实。这看起来可能很复杂，但是您可以使用正确的工具快速实现优化。

**用**[**page detox**](https://pagedetox.com/?utm_source=devto&utm_content=performancescore)**分析你的网站，开始吧。**

然后通过使用 webp 图像、压缩图像和响应图像来消除所有问题并提高分数:

[![rsedykh image](img/05c843600006315d5bb2612a7fa1571d.png)](/rsedykh) [## 使用 Uploadcare 优化自动驾驶仪上的图像(以提高页面加载速度)

### 罗曼·塞迪克 2 月 15 日 19 分钟读

#imageoptimization #pagespeed #beginners #webperf](/rsedykh/key-image-optimizations-to-maximize-your-google-pagespeed-score-a-guide-by-pagedetox-2hfp)