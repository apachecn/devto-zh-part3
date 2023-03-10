# 网站开发者可以从网络星期一十大电子商务网站中学到什么

> 原文：<https://dev.to/theblairwitch/what-web-devs-can-learn-from-the-top-10-ecommerce-sites-on-cyber-monday---------2ko8>

最初，我计划只监控网络星期一十大电子商务网站的网络和页面性能指标。但是在分析了这些数字之后，我发现了一些我没有预料到的事情...第三方跟踪标签对页面加载时间造成了巨大的负担。

[![Number of tracking tags vs page load times](img/554ec765c71c8f862c34a4da9bdff31e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JtlinI5B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh6.googleusercontent.com/reqQ4pHde3vbW0wuYvCxzwal_I2jXd8A0OZQJgWhFGq-xkQpfyWCGqS553L-7Zg7vz9f4-yESViWldsUKvBJZpAFWpHtPImcxWj9HK1fdYc9ua6Tn2OV72RonA3BX-oJuJ2XR0wy)

上图显示了 DOM 加载时间从短到长的前十个站点。我注意到有更多追踪器的网站总是有更长的 DOM，总负载和完成时间。

实际上，第三方追踪器对加载时间的影响比请求或页面大小更大！一些网站需要一分多钟才能完全加载，因为它们在等待跟踪信标的请求。

你可能知道这些追踪器是像素或信标。它们是非常小的脚本，捕获网站访问者的信息，并将这些信息发送到外部应用程序进行报告。

您很可能在您的网站上使用了一些这样的跟踪器，并认为由于它们是进行快速外部调用的简短脚本，并且不会改变 DOM 中的任何内容，所以它们一定是无害的。是也不是…

追踪器本身通常不会对用户体验产生任何影响，因为它们在后台运行。但是，当您有几十个第三方跟踪请求，并且每个请求需要几百毫秒的加载时间时，您可能会增加宝贵的加载时间。

在查看了排名前十的网站后，我发现这些网站平均使用了 24 个追踪器，最高时在一个页面上使用了 74 个追踪器。这 74 个请求可能会阻塞加载时间或阻止其他资源进行渲染。

现在，这些网站并不能代表网络的主流，甚至不能代表你可能正在处理的项目。但重要的是以这种规模来看待网站，这样我们就可以看到小事情是如何堆积起来并真正影响用户体验的。

在我们深入探讨之前，我们需要讨论不同种类的加载时间。我们有三种不同的方法来衡量页面负载:

*   **DOM Load:** 浏览器渲染所有 HTML 元素和脚本需要多长时间。
*   **页面加载:** DOM 加载+下载所有页面内容(图片、视频等)所需的时间。)
*   **完成:**发送和接收页面上所有请求所用的时间。

第三方追踪器通常在 DOM 加载后才会加载，这就是为什么您会看到 DOM 加载和完成时间之间的巨大差异。

例如，亚马逊的 DOM 加载只花了 1.55 秒，而总加载时间超过了一分钟。当我们在启用广告拦截器的情况下再次运行结果时，完成时间下降到 45 秒。

现在 45 秒仍然是一个很长的完成时间。罪魁祸首是单个资源，加载时间超过 30 秒，不可避免地会失败。在这种情况下，DOM 加载没有受到影响。

亚马逊绝不是唯一受第三方追踪器影响的网站。在第一轮测试完成后，我回去在启用广告拦截器的情况下重新运行了所有测试。

我使用的是 Chrome 浏览器的扩展版本 [Ghostery](https://www.ghostery.com/) ，它可以防止广告出现在网页上，并限制追踪器抓取敏感信息。

[![Ghostery Screenshot](img/c5b131d688b7c5590bb724006de07b70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U3qPve7v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/ZIEk5L2vBTDlLGZUA-7mLMcVCHXBJ5uqusyY25wF3ugP61xbj3UIRo2L0lka-Nm9b724IxnECFEqInxj4WodL0429W2MJnoK4qwQUtj8yQn8cjH2TwNl06bgE1BL4oOgUQeRh3qu)

我还使用了 Sonar Lite，这是一个免费的 Chrome 扩展，使用实时监控来捕捉页面负载和请求时间。不要脸的塞，我也为康斯泰利克斯工作..但是这是一个非常棒的免费工具，它比打开开发工具和每次我需要性能指标时硬重新加载页面更容易。

[![Sonar Lite Screenshot](img/b24ebb363aa8db8884051227dfb794f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9WNTrbM_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/VoQ8iN837MQy6zmmyYpZ1axnMb6drspHVQ0CNyhdRSMXN8cnPDDOFVr1Xhpodk55sB6FZ7OQzokXHlllCdKdUDuLRvpJM5qyTihmpWLyz_XwOQ0Jmqm-NrLQWmvOYzv6w7kEbPua)

在大多数情况下，我看不出有没有广告拦截器有多大的审美差异，只有美国柯尔百货公司例外，他把自己的网站背景变成了广告。一旦我启用了 Ghostery，它就完全消失了。

[![Screenshot of Kohl's homepage with and without Ad Blocker](img/c4ddfd3d5fc6239d2efcf92ef8f300b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d_PVmXZD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh4.googleusercontent.com/90Kd5bijqkikcsh40RQjCkSO9v65Y0Rywpxhn0PHDosMvJWJnyFwz1mzE71yzMgR2m2Vdyft0HZCyodQLYg7Pn82ju5GUEWf7DVeEiQ0qgSn5e2KnXtqmHzNAFObbxyiypobxz0G)

花了很长时间渲染初始绘画的页面(在浏览器中渲染视觉元素需要多长时间)仍然花了同样多的时间。

所有这一切都在意料之中...直到我计算了这些数字，并与第一次测试进行了比较:

*   当我们使用广告拦截器时，DOM 负载平均减少了 8.9%。
*   总页面负载(包括图像)减少了 17%。
*   页面尺寸缩减了 24%
*   请求减少了 43%
*   第三方追踪器减少了一半，下降了 58%

[![Performance of top eCommerce sites with an Ad Blocker](img/e0b4406991a88344cbf9bafdf809333d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W2Icl5hk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/0K5O20KeP1wVoGseAWID5kxXNuMCBWPyW1aKt5a9RrqKhertjOEaILtN1kk3qCDmX4PiLL1xZvVOeknWNDwhJolRW9Bq9C-GnAhf-UXu4Tgak3ytLLm-qq72m63zi8D_b0pkWILU)

你会注意到追踪器的数量没有降到零。即使我们启用了广告拦截器，仍然有一些追踪器在使用，因为 Ghostery 认为这些追踪器是“必不可少的”，我们所有的数据在发送之前都是匿名的。

但是当您删除大部分跟踪器时，所有三个负载指标、请求数量和页面大小都显著下降。

那么，这对你有什么关系呢？如果你使用第三方追踪器，你需要意识到对性能的影响。

我们在这项研究中监控的网站是最佳网络性能的缩影。他们虔诚地使用(并建立)web 性能和高效 web 开发的最佳实践。尽管如此，由于过度使用第三方跟踪脚本，他们仍然和其他人一样容易犯同样的错误。

当我在移除跟踪器的情况下重新运行测试时，平均 DOM 加载时间从 1.9 秒下降到了 1.7 秒。这似乎无关紧要，但当你有数十亿人同时使用你的网站时，每一毫秒都很重要。

专家估计超过 7500 万购物者将会在网络星期一购物。对于我们监测的十家零售巨头来说，可能有几百万购物者在 24 小时内浏览他们的网站。

如果页面加载量下降一丁点，就可能损失数千美元的收入。

这就是为什么这类网站是最好的学习来源。他们有几十人的团队致力于保持网站的精简和高效，这一切都是为了确保当他们受到假日购物者的挑战时，性能不会下降。

即使你没有这些电子商务巨头拥有的资源或人力，你仍然可以从他们那里学到很多东西，并应用到你自己的开发项目中。

如果你从这篇文章中学到了什么，那就是留心你使用的追踪器。每个像素都可能影响性能，所以不要轻视这些小脚本。

### 关键要点:

*   有更多追踪器的网站倾向于有更长的 DOM 和完成加载时间。
*   然而，有更多请求的站点与更长的加载时间没有关联。
*   没有完成所有请求的页面加载时间更长。
*   延迟加载(滚动加载图像和脚本)将页面加载速度提高了几百毫秒到几秒钟
*   60%的顶级网站的 DOM 加载时间低于两秒。
*   网站大小不同寻常地高于往年，平均为 3.75 Mb，几乎是推荐大小 2Mb 的两倍。
*   追踪器平均占网站大小的一兆字节。

### 结果:

这是两组测试的结果。当我使用广告拦截器时，每个单元格包含第一组测试和第二组测试之间的差异。红色单元格表示加载时间或请求/跟踪器数量增加。绿色单元格表示当我们使用广告拦截器时，加载时间或请求/跟踪器数量有所改善和减少。

[![Difference between using an Ad Blocker and not](img/62db61f958083e0fe1aa9612fc1d4b8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yc39cxDf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh6.googleusercontent.com/rMKacyUerTB1A4UYRoc_J_L9FLghc7x_BO8nmGq4Of1F19mAb4g8fEevnDTUqxUHQWOQ87Ze1j2YlOPVOZ-ZMWNQ6uWOn-I1CDM5T_acY4DCjjvRiQ0RJYyATQDRyiWaFXeZP0oH)

让我知道你对这项研究的看法。你对结果感到惊讶吗？你在数据中看到了什么我没注意到的东西吗？我很想听听你的发现。