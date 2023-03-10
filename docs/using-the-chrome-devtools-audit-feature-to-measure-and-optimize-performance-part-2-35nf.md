# 使用 Chrome DevTools 的审计特性来测量和优化性能(第 2 部分)

> 原文：<https://dev.to/jdmedlock/using-the-chrome-devtools-audit-feature-to-measure-and-optimize-performance-part-2-35nf>

#### 调优应用

<figure>[![](img/e79a9e00fe2ec5e1e9fd5a310b183798.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BDVT65PV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHZKjpFzH3qxMSVBO16heJg.jpeg) 

<figcaption>照片由 rawpixel.com 发自 Pexels</figcaption>

</figure>

在第 1 部分中，我们提出了一些问题来帮助构建应用程序性能问题，定义了一个性能调优，并演示了如何使用 Chrome 开发工具的性能审计特性来创建性能基线。

在本文中，我们将使用陨星浏览器前端 web 应用程序来展示如何识别、测量和纠正特定的性能问题。

陨石探索者 repo 托管在 [GitHub](https://github.com/jdmedlock/meteorite) 上，在执行任何调整之前，起点位于`feature/01-initial-app`分支。

### 查看应用程序的性能

性能调优的关键在于理解应用程序的运行方式，并清楚了解其性能属性。这两者都是必要的，因为即使您可能对应用程序的内部工作原理有深入的了解，但可能不清楚不同部分如何交互以及它的伸缩性如何。

作为一个开发者，也可能是这个应用的创造者，你需要理解它是如何做的。这包括理解应用程序的性能属性，但是使用审计特性可以简化这项任务。

每个应用程序都是独一无二的，并且有可能出现特殊情况，这可能会使解密变得非常棘手。然而，回顾 Dev Tools 性能审计并遵循它提供的建议是一个很好的起点。

#### 步骤 1 —审核审计指标

让我们重新检查一下陨石资源管理器应用程序的性能审计报告的度量部分(参见图 4)。尽管陨石探索者的整体性能分数看起来可以接受，但我们可以看到两个需要纠正的地方——糟糕的速度指数和高输入延迟。

速度指数低表示页面呈现速度非常慢，而输入延迟高表示应用程序对用户输入的响应速度很慢。附带的指标证明了这一点，该指标显示从启动到显示有意义的信息花费了 6.5 秒。此外，还需要 0.9 秒来响应用户输入。

跟踪(参见图 5)将用户在屏幕上看到的内容与主线程上的活动进行了比较，这强调了渲染速度是一个问题。该跟踪的摘要部分显示 Javascript 花费了 3.7 秒，将光标悬停在“微任务”栏下的主线程时间线区域上显示该时间的大部分用于执行`DOMNodeInserted`事件。

当我们开始确定可能的改进时，将重新审视这两个事实，即应用程序中花费时间最多的领域。

#### 第 2 步—回顾机会

实施绩效审计所强调的机会(见图 6)是“易得之果”尽管节省可能很少，但 Performance Audit 通过识别问题、建议解决方案和提供节省估计值，为我们做了艰苦的工作。

对于陨星浏览器，减少客户端和主机之间检索陨星着陆数据的往返次数可以额外节省 0.3 秒。在这种情况下，从[https://data.nasa.gov](https://data.nasa.gov.)检索包含陨石降落数据的 JSON 文件。

与审核指标的审查一样，此时不会采取任何措施来解决此问题。相反，我们将在确定可能的改进后再回来讨论它。

#### 步骤 3 —查看诊断

审查应用程序性能的最后一步是检查诊断列表(参见图 7)。陨石探索者的诊断是，

> 使用过大的 DOM 大小— 10，073 个节点

回想一下，我们在审查审计指标时发现有大量的`DOMNodeInserted`事件。将这个事实与这个诊断结合起来表明，减少我们添加到 DOM 中的节点数量可以减少 3.7 秒的运行时间。

> 主线程工作过多— 18，540 毫秒

在这个概要文件中，应用程序的主线程使用了 18.5 秒。我们知道其中的 3.7 秒用于向 DOM 添加节点，但是剩下的时间花在哪里了？单击此诊断可提供该时间花费的明细。

<figure>[![](img/22df3e33e3e368a74db54a33780592f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aQjOVZQV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/710/1%2AP2O25aU1l9BgoXF-MzLZVQ.png) 

<figcaption>图 10 —审计诊断</figcaption>

</figure>

同样，知道插入 DOM 节点需要相当多的时间直觉告诉我们，主线程中很大一部分时间用于更新 DOM。

> Javascript 启动时间太长— 4，660 毫秒

知道 Javascript 启动时间过长很重要，但更重要的是理解为什么会这样。通过单击诊断，可以按总时间降序显示启动期间访问的 URL。

<figure>[![](img/afb94925c04eb61d6336436a0a9ecbcf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O0jiTb9J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/710/1%2Apn0ZGCkpPsf66xOFKIu-6Q.png) 

<figcaption>图 11 — Javascript 启动时间诊断</figcaption>

</figure>

前两个 URL 几乎占了启动所需时间的一半。第一个是语法插件，第二个是我们的应用程序。后者被称为应用程序，因为它驻留在 repo 的构建目录中。用于在此目录中创建生产应用程序的 npm 运行构建命令。

<figure>[![](img/893fbcfe0ab8dba8b843b1cc764a4cc2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FzROi4ZM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/466/1%2AbkfwLumAbpXuJDhTL93a9g.png) 

<figcaption>图 11 —构建目录</figcaption>

</figure>

> 对静态资产使用低效的缓存策略—发现 3 项资产

该诊断表明，通过缓存应用程序所需的静态资产，而不是在每次需要时检索它们，可以节省时间。此时，我们知道这三个都是应用程序的一部分，因为它们位于 repo 中的构建目录中(参见图 11)。

如果您不熟悉如何创建性能缓存策略，诊断信息中的“了解更多”链接是关于此问题的背景信息和解决方案的绝佳来源。

<figure>[![](img/8fe60c84b167350ad2b9472eeae32192.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s62PUqpl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/710/1%2AiRkq8N-AactpucEO6eAZrQ.png) 

<figcaption>图 13 —低效缓存策略诊断</figcaption>

</figure>

> 关键请求链—发现 3 个链

最终的诊断确定了三个以高优先级运行并且有点长的请求链。正如我们在回顾缓存策略诊断时发现的那样，这三个链都是我们的应用程序逻辑的一部分(参见图 11)。

该审计旨在指出改善页面加载时间的机会，而不是绝对的“通过”或“失败”条件。

<figure>[![](img/9b55bad592503b82b752ab23186cb752.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8H133HN0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/710/1%2A7aQ_Y3jdyQGtYfBmMLDxQg.png) 

<figcaption>图 14 —关键请求链诊断</figcaption>

</figure>

### 确定可能的改进

<figure>[![](img/fd5cbc300885467fddf60547a74ca661.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_L9l1pM4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0ibnfzRqKjE7XC3iXJ_oaA.jpeg) 

<figcaption>照片由[万花筒](https://unsplash.com/photos/26MJGnCM0Wc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/search/photos/idea?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

在审查审计指标、机会和诊断之后，总结和优先考虑重要的发现总是一个好主意。就像一个教练为一个运动队安排比赛一样，组织事实建立了一个起点，确定我们想要进行哪些改变来提高应用程序的性能。

现在的任务是确定并优先考虑在审查陨石探测器性能期间确定的每个性能观测的解决方案。优先顺序是至关重要的，因为我们将按照优先顺序处理性能问题，直到我们达到收益递减点。

> 速度指数差— 6.9 秒

跟踪显示应用程序在处理`DOMNodeInserted`事件上花费了相当多的时间，再加上诊断显示 DOM 过大、主线程工作量过大以及 Javascript 启动时间过长，这些都清楚地表明 Meteor Explorer 对 DOM 的操作效率低下。

正是在这一点上，对应用程序逻辑的深入了解发挥了作用。在 App.js 中，`componentDidMount`函数同步加载陨星登陆 JSON 文件，并在完成
时将`isDataLoaded`状态变量设置为真

<figure>

```
 componentDidMount() {
    fetch(process.env.REACT_APP_METEORITE_STRIKE_DATASET)
    .then((response) => {
      return response.json();
    })
    .then((strikesJSON) => {
      this.setState({ meteoriteStrikes: strikesJSON });
      this.setState({ isDataLoaded: true});
    });
  } 
```

<figcaption>Figure 15 — componentDidMount function in App.js</figcaption>

</figure>

一旦陨石着陆文件被加载，它的内容，所有 46K，被添加到`<MeteoriteTable>`组件。

<figure>

```
<section className="App-results">
  <div>
    {this.state.isDataLoaded
       ? ( <MeteoriteTable meteoriteStrikes={ this.state.meteoriteStrikes }
             searchTerms={ this.state.searchTerms } /> )
       : (' ')
    }
  </div>
</section> 
```

<figcaption>Figure 16 — Populating rows in the MeteoriteTable component</figcaption>

</figure>

将进行两项更改来解决这个性能问题。

1.  *解决方案*:实现一个[服务器](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)来缓存陨石降落数据，以减少往返 Nasa 主机的次数。*预期结果*:将高 Javascript 启动时间减少到可接受的水平。
2.  *解决方案*:更改`<MeteoriteTable>`组件支持分页，减少 DOM 节点数量。*预期结果*:将 DOM 节点总数和主线程所需时间减少到可接受的水平。

在这两种情况下，成功的衡量标准是将糟糕的速度指数恢复到绿色状态，并消除过大的 DOM 大小、过多的主线程工作和 Javascript 启动时间过长的诊断。

> 输入延迟— 0.9 秒

阅读该问题的补充信息可以发现，性能审计功能将[滞后](https://en.wikipedia.org/wiki/Lag)应用程序定义为响应用户输入的时间超过 100 毫秒的应用程序。

同样，对应用程序的了解是确定可能的解决方案的重要因素。陨星探索者的搜索功能使用 Lodash 函数去抖来间隔，或 [*去抖*](https://css-tricks.com/debouncing-throttling-explained-examples/) ，用户按键产生的调用，以消除同一按键的多次调用。

```
this.emitChangeDebounce = debounce(this.queryName, 150); 
```

此问题是处理搜索词输入时等待 150 毫秒的结果。

*解决方案*:将去抖等待时间减少到 95ms。*预期结果*:消除输入延迟问题，不影响用户体验。

> 低效的缓存策略和关键请求链—未知

这个问题最可能的解决方案是因为没有正确指定缓存控制策略。缓存控制是一个复杂的课题，在这个[教程](https://www.mnot.net/cache_docs/#CACHE-CONTROL)里可以学到更多。

图 17 显示，在缓存中，最大年龄设置为 600 秒，控件的最大年龄为 600 秒。在应用程序不经常改变的生产环境中，这个设置太低了。

<figure>[![](img/cb2a4b953380378bb5d29dd6f1c9e3b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OtTwe-RY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/711/1%2ALBm0v52M5exBqFqQKAcoqw.png) 

<figcaption>图 17 —缓存控制设置</figcaption>

</figure>

*解决方案*:将缓存控件中的最大年龄设置增加到 86，400 秒，即一天。*预期结果*:消除“低效缓存策略”诊断。

为了纠正“关键请求链”诊断，有必要改变 App.js 检索陨石着陆数据的方式。因为 JavaScript 是单线程的，所以不能像其他语言那样为特定的子任务创建新的线程。然而，异步执行活动将消除 UI 活动的阻塞。

*解决方案*:改变 App.js 中的函数`componentDidMount`异步检索陨石降落数据。*预期结果*:消除“关键请求链”诊断。

### 测试改进思路

<figure>[![](img/a8f12faf5741949d2d0fe66330be8664.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i_kpfT-c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Az7aahOChZZxNfMInuGUN4w.jpeg) 

<figcaption>照片由[尼古拉斯·托马斯](https://unsplash.com/photos/3GZi6OpSDcY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)于[上](https://unsplash.com/search/photos/crash-test?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

一旦确定了前三个问题的解决方案、预期结果和成功标准，就到了实施它们的时候了，看看它们是否有预期的结果。以下步骤将用于解决这些问题，

1.  为每个解决方案创建一个分支。
2.  将对应用程序进行测试，以确保更改不会影响其功能。
3.  将运行新的绩效审核，并与原始审核进行比较，以确定绩效影响。

> 解决方案#1 —实施服务人员(分支`feature/02-service-worker`)

Create React App 已经包含了对在生产模式下运行的应用程序的服务人员支持。默认情况下，服务人员在`index.js`中被禁用，但是可以通过将`serviceWorker.unregister()`替换为`serviceWorker.register()`来启用。

为了测试服务工作者的影响，使用 npm 运行 publish 命令将分支`feature/02-service-worker`发布到 GitHub 页面，并重新运行性能审计。

重新运行性能审计显示，服务人员不仅减少了速度指数时间，而且将其恢复到可接受(例如，绿色)状态。

<figure>[![](img/f33b05ffdee4d8c3b4f9150a9af905aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fx43jS8D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/714/1%2ApRwe3CUU0y-zahMcNK0s9Q.png) 

<figcaption>图 18 —服务人员结果</figcaption>

</figure>

请注意，估计的输入延迟增加了 192 ms，但是，由于我们知道稍后会减少去抖等待时间，所以我们将拭目以待，看看它对延迟有何影响。

> 解决方案# 2——向`<MeteoriteTable>`组件添加分页(分支`feature/03-pagination`)

DOM 中的节点数量过多，因为陨石资源管理器无条件地在`<MeteoriteTable>`组件中添加所有陨石降落。向表中添加分页将通过限制一次显示的行数来纠正这一问题。

<figure>[![](img/0c54b38552073ccd11113f49c01cd3f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BOL3l-Lq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AePCrWl7RjM5yQwYdXsjGKA.png) 

<figcaption>图 19 —分页 UI 变化</figcaption>

</figure>

实施此更改后，不再将“使用过大的 DOM 大小”作为需要更正的诊断信息。

<figure>[![](img/177899fc9f718f96820da9651e5a8f18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MzD8sOEe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/714/1%2AQsE0icitD7iDxq_x_EnREw.png) 

<figcaption>图 20 —分页结果</figcaption>

</figure>

> 解决方案#3 —将`debounce`等待时间减少到 95 毫秒(分支`feature/04-debounce`)

有时候解决方案达不到预期的效果。在这种解决方案中，将去抖等待时间从 150ms 减少到 95ms，然后再减少到 75 ms，并没有像预期的那样减少输入延迟。然而，检查度量跟踪表明，语法浏览器扩展消耗了大量的时间。

<figure>[![](img/581d0316215bebd611e0315bb1b1f6d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GunoIpdU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/712/1%2ABlHwOyqj1M1zz75Gxh4o7Q.png) 

<figcaption>图 21 —语法上的主螺纹痕迹</figcaption>

</figure>

禁用这个扩展并没有达到解决输入延迟问题的预期效果，但是整体健康指标已经上升到 98，JavaScript 启动时间和“关键请求链”诊断已经降低到可接受的水平。

<figure>[![](img/b0e90a61da1c5c04f121c37a24c85751.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m-TG1sHs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/711/1%2AxhfFaIzxfqWH2XR-QyIDZA.png) 

<figcaption>图 22 —语法禁用后的结果</figcaption>

</figure>

> 解决方案 4 —低效的缓存策略和关键请求链

[![](img/494e61dcbf9872235db34d4db3a97cd3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wwK7Lrrr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/625/1%2ADhBv84c_sPxiH1qisUgWaw.jpeg)

“低效缓存策略”诊断应该通过更改来自服务器的 HTTP 请求头，将陨石浏览器的`cache-control`的`max-age`设置增加到 86400 秒来纠正。然而，这是一个客户端专用的应用程序，因为我们使用 GitHub Pages 作为我们的主机，这是不可用的。

相反，尝试将以下内容添加到`index.html`中，但是没有效果，因为服务器将最大年龄设置为 600。

```
<meta http-equiv="Cache-Control" content="must_revalidate, public, max-age=86400"> 
```

类似地，对`App.js`中`componentDidMount`的更改也没有达到消除“关键请求链”诊断的预期效果。即使在将陨石着陆数据的获取从同步改为异步之后，这种诊断仍然存在。

图 23-异步陨石降落数据检索

尽管我们无法消除这些诊断，但通过调整，应用程序的性能明显提高了。其影响是，绩效审计所衡量的总体绩效评级从 72 增加到 99。

<figure>[![](img/77c2f97ab3b6386a19d6148dc021a919.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lu1pEbl6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/549/1%2Av2LQiwkbnMONIe8hqgLW5A.png) 

<figcaption>图 23 —结局结果</figcaption>

</figure>

然而，不应该忽略解决“低效缓存策略”和“关键请求链”诊断的失败，因为这样做会增加技术债务。尽管我们目前正处于应用程序收益递减的阶段，但随着应用程序的增强，这些收益可能会变得更加重要。

为了将来做好准备，需要进行额外的调查和研究，以了解更多关于这些诊断的信息，并确定如何解决这些问题。敬请关注…

### 包装完毕

[![](img/76bebc4bb40ff746d73e6ba07623122f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M950G044--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2ALAYIosRHBjLj1vGs8yWxPg.gif)

创建一个高性能的应用程序并不是偶然发生的。这需要思考、方法和努力。在应用程序的生命周期中保持性能的承诺要求对每个新版本进行测量、监控和改进。更重要的是，它需要将性能视为用户体验和满意度的一个重要方面。

与保持用户体验质量同样重要的一个额外的好处是一个客观的、可重复的过程对开发人员个人发展的影响。观察、发展想法并测试它们会强化你所知道的正确的东西，并揭露不正确的东西。

正如我们在上一节中看到的，关于如何修复两个诊断的一些假设是不正确的。这不应该成为消极因素。相反，这是一个学习的机会，也是学习更多的动力。

犯错和从错误中学习的能力是你的开发武库中最强大的工具之一。虽然有人说“你是自己最大的敌人”，但反过来说，你才是自己最宝贵的财富，这也是事实。利用这一点，让你自己有被科学蒙蔽的自由！

***披露*** :本文基于之前的一篇文章“[使用 Chrome DevTools 审计特性测量和优化性能](https://medium.com/chingu/get-blinded-by-science-and-boost-application-performance-b5afd64e34d4)”，该文章被重构为两个部分。

* * *