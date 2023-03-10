# 每周网络平台新闻#1

> 原文：<https://dev.to/simevidas/weekly-web-platform-news-1-4edk>

*这是从 2 月 6 日到 2 月 11 日发表在 [https://webplatform.news](https://webplatform.news) 上的新闻精选。*

* * *

*   [尼古拉斯·霍伊泽](https://mobile.twitter.com/nhoizey/status/1092551058411454465):网络工具包漏洞已经被重新打开。CSS `100vh`值大于移动浏览器中的视口高度。

* * *

*   [DuckDuckGo](https://spreadprivacy.com/do-not-track/) :包括谷歌、脸书和推特在内的大多数主要科技公司都不尊重<mark>不跟踪</mark>设置(`DNT: 1`请求头)，这一设置被大约 24%的人使用。苹果将在 Safari 的下一个版本(12.1)中移除这一设置。

* * *

*   [尼克·科利](https://mobile.twitter.com/keithjgrant/status/1093209933456330752):你可以通过在`@media (hover)`规则中定义你的`:hover`风格来防止触摸设备上的<mark>【粘滞悬停】</mark>效果。[ **演示**

* * *

*   [Jen Simmons](https://mobile.twitter.com/jensimmons/status/1093313475563134977):CSS 工作组目前正在讨论触摸设备上的<mark>虚拟键盘</mark>是否应该影响视口高度(包括垂直媒体查询和`vh`单元)。

    **注意:**在 iOS 上，虚拟键盘*对视窗高度(`window.innerHeight`)和`vh`单位*没有影响，但在 Android 上有影响(那些值变小了)。演示:[https://jsbin.com/mocepom/quiet](https://jsbin.com/mocepom/quiet)。

* * *

*   [HTML 标准](https://mobile.twitter.com/simevidas/status/1093916793985486848):带有`target="_blank"`的链接现在默认为<mark>`noopener`</mark>。

    [![](img/5ad90c2f36d117f2cb763795a21096c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gxEBBghX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webplatform.news/media/html-standard-implied-noopener.png)

    **注意:**这个变化已经在 Firefox Nightly 和 Safari 技术预览版中实现了。如果您在外部页面的链接上设置了`target="_blank"`，请确保也设置了`rel="noopener"`以防止目标页面访问您的页面(通过`window.opener`)。

* * *

*   [Patrick Hulce](https://mobile.twitter.com/patrickhulce/status/1093961183562997760):web 上流行的<mark>第三方脚本</mark>的平均执行次数(基于 HTTP 存档抓取数据和 Lighthouse 测试结果)。

    [![The average execution time of some popular third-party scripts](img/2d3f4f1b63129aee190a5f1c7148ac9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eEZCAz2L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webplatform.news/media/third-party-scripts-average-execution-time.png)

* * *

*   [Jen Simmons](https://mobile.twitter.com/jensimmons/status/1094731679040712707):CSS<mark>`vertical-align: baseline`</mark>对于`<img>`元素来说并不是一个好的默认设置，因为它会在图像下方造成一个小的间隙(行框的基线和下行线之间的空间)。

* * *

*访问 [https://webplatform.news](https://webplatform.news) 获取最新的 web 开发新闻。*