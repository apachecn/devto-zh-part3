# NGConf 2019 -主题演讲第一天

> 原文：<https://dev.to/angular/ngconf-2019-keynote-day-1-4295>

## 免责声明

这是我个人对 ngconf 会议的总结。虽然我用自己的话总结这些东西，但所使用的材料，如图像、图表、源代码示例都不是我自己的。其中大部分来自 Youtube 视频或各场会议发言人的幻灯片。

## 其他时段？

这篇文章是从[我的博客](https://juristr.com/blog)交叉发布的。如果你想阅读原文，也包括其他会议，前往[原文](https://juristr.com/blog/2019/05/Angular-8-and-the-Future-NGConf-2019-Roundup/?utm_source=devto&utm_medium=crosspost)。

在 twitter 上关注我。

## 主题演讲(第一天)

*布拉德·格林&伊戈尔·米纳尔-[Youtube](https://youtu.be/O0xx5SvjmnU)T3】*

首先，在继续“技术细节”之前，这是 Angular 社区所代表的..

[![](img/635ba2b2bfe2dad152041cdd4ffc115c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YYCKT3gv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/angular-values.png)

..我可以百分百确定。尤其是社区方面:我已经在这个社区呆了几年了，参加了很多会议，在会议上发言，所有人都非常热情、友好和乐于助人。

Angular 是一个平台，不再仅仅是一个框架，它包含了许多产品。

[![](img/f21c0c816c8070b4c9a4dcbcd1fa8905.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bAhLTte5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/angular-products.png)

你不必全部都用上。您可以根据自己的需要插入它们。背后的棱角分明的团队，确保他们都很好地配合在一起，周围的工具是建立在最佳集成，使你最有生产力。

Angular 版本 8 目前(在撰写本文时)处于 RC 模式，将于 5 月底发布。

[![](img/f9c5b4d7c65fc26db9c7ec6347941c98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eBpd03CE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/angular-release-timeline.png)

### 差动加载

v8 即将推出的主要功能之一是**差动加载**。一般来说，谷歌的 Angular 团队和 Chrome 团队都非常重视性能。在其他优化技术中(如[参见我的惰性加载文章](https://dev.to/blog/2019/04/Lazy-Loading-Components-with-Angular-Elements/))，差异加载创建了两个不同的包:一个用于遗留浏览器，另一个(减少了填充)用于现代 evergreen 浏览器。

[![](img/4abd82fafc8c73331ad90e4a643f9bad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K6pMG03c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/differential-loading.png)

这允许节省大约 7-20%的当前包大小。

但是还有更多的功能即将推出:

[![](img/3cea3d40c113a8a2862c69bd49b00aa4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rez7fhXV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/features-v8.png)

### 建设者

**构建器**是稳定 API 的新特性，也称为“架构师”，允许扩展当前的 Angular CLI 构建流程。像 Angular Schematic 允许你扩展和挂钩到 CLI 的代码脚手架进程，允许你提供自己的生成器， *builders 允许你定制 CLI* 。

如果你查看你的`angular.json`文件:
，你已经看到那些建造者了

```
{  "$schema":  "./node_modules/@angular/cli/lib/config/schema.json",  "version":  1,  "newProjectRoot":  "",  "projects":  {  "demoapp":  {  ...  "architect":  {  "build":  {  "builder":  "@angular-devkit/build-angular:browser",  "options":  {  ...  },  },  "serve":  {  "builder":  "@angular-devkit/build-angular:dev-server",  ...  },  }  }  },  } 
```

Enter fullscreen mode Exit fullscreen mode

这为您自己决定应该运行哪些工具打开了一个巨大的可能性集，并且您可以自己编排它们。像构建器一样，您可以创建直接从 CLI 自动部署的功能。

### 网络工作者和懒加载

Web Workers 支持得到了改进，允许将一些密集型处理转移到一个单独的工作线程中，以避免干扰浏览器的主 UI 线程。

**惰性加载**也将最终不再依赖 Angular CLI 必须了解、解析和应用代码拆分的“神奇字符串”。你可以在路由器的惰性加载配置中看到这些字符串:

```
// example lazy route
{
  path: 'search',
  loadChildren: './search/search.module#SearchModule'
} 
```

Enter fullscreen mode Exit fullscreen mode

从 v8 开始，你可以使用原生的`import()` API，并像
一样重写它

```
{
  path: 'search',
  loadChildren: () => import('./search/search.module').then(mod => mod.LazyModule)
} 
```

Enter fullscreen mode Exit fullscreen mode

### 在制品:巴泽尔和艾薇

还有一些工作正在进行中:

[![](img/b2570e715939fe142ef1840aeda3fb4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--izNqWwBT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/angular-version-9.png)

巴泽尔就是其中之一。这是谷歌内部“火焰”构建工具的开源版本，为其庞大的内部 monorepo 提供动力。Bazel 以速度极快而闻名，尤其是因为它允许在云上扩展的能力(谷歌有一个内部要求，即任何代码库的重建平均不应超过 3 秒😱)

[![](img/cab13543413b309787923918e0116d35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qeKdTJ9q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/bazel-features.png)

Angular 团队中的一个专门团队目前正在努力将 Bazel 引入 Angular 社区。我们都可以从这样一个超级构建工具中受益。目前，它仍然是一个“早期版本”，可以使用和工作，但仍然有点粗糙。到 Q9 时，目标是每个人基本上都能够在 CLI 配置上启用一个标志，Bazel 将负责构建而不是 Webpack(类似的东西)。具体细节仍在研究中)。

到目前为止，在谷歌内部，通过使用 Bazel 可以观察到一些非常大的改进:

[![](img/b2116757d1f69b235a69bddc24441144.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--73Z0Nhkm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/bazel-build-time-reduction.png)

当然还有新的**常春藤渲染器**。以下是它的目标:

[![](img/d37294be954b39e5f783a24c08570746.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k3-FwHtQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ivy-features-overview.png)

v8 当前的重点是保证**向后兼容**。最重要的是， **Ivy 在 v8** 中不会默认开启。该团队将其与 v8 一起发运，并有可能选择加入。目前谷歌内部 97%的单元，集成和截图测试目前都通过。

[![](img/f7cd46935086f592e9d4d27e37f1cace.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V1HQup-2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ivy-tests-google.png)

这使得该团队非常有信心，但是他们正在利用时间对第 9 版进行调整(今年第 4 季度),以获得来自社区的进一步反馈。

[![](img/18e76354c908c49f41134e35788ddd14.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AWzddeaz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ivy-backwards-risks.png)

Angular 团队有兴趣获得具有许多不同依赖关系的项目的反馈，以了解这些项目与 Ivy 的合作情况

也就是说，你绝对可以选择使用你的 Angular v8 应用。你可以通过用
生成一个新的 v8 应用程序来尝试 Ivy

```
$ ng new my-app --enable-ivy 
```

Enter fullscreen mode Exit fullscreen mode

..或者通过在您的`tsconfig.app.json`
中添加`enableIvy`在现有的 v8 应用上启用它

```
{  "compilerOptions":  {  ...  },  "angularCompilerOptions":  {  "enableIvy":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

所有的细节都可以在[https://next.angular.io/guide/ivy](https://next.angular.io/guide/ivy)上找到。

### 常春藤特征

那么 Ivy 带给我们最重要的是什么呢？首先是**较小的捆**。

[![](img/2fe8bfca740bc8dcb51dc1a7af0a4cc7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pTzocak9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ivy-size-chart.png)

为了解释这个图像:

*   视图引擎是当前的渲染引擎
*   视图引擎+差分将使用当前的渲染器和 v8 中引入的新差分加载
*   Ivy Compat + Differential 是您在激活`enableIvy`标志时得到的，这是团队最需要反馈的地方。
*   Ivy Future API + Differential 是一个实验性的、还没有文档化的 API，它将极大地减少 API 的大小。

除此之外，Ivy 还有一些非常好的副作用:

*   **更低的内存需求-** 团队观察到应用模拟减少了 30%,而 Angular Material library 测试占用的内存减少了 91%
*   **测试速度更快-** 角框架单元测试时间减少 38%，素材库减少 80%
*   *加上很多突出的 bug 已经被修复*

### 棱角分明的绿色

[![](img/18ca7a6dc4c5d33cd61b4b28c235db01.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ehHxcrKE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/angular-evergreen.png)

Angular straight 从一开始的目标之一就是成为“常青平台”。如果你考虑到快速发展的网络技术，这是一个相当疯狂的目标，对吗？但是现在我们已经到了第 8 版，而且我们从公司那里得到报告说，升级跨越了“潜在的破坏版本”,没有任何麻烦，而且是在创纪录的时间内。刚刚看了[法航荷航的帖子](https://medium.com/airfrance-klm/how-we-upgraded-a-website-with-half-a-billion-annual-users-to-angular-7-in-less-then-a-day-71de33a1108e)。

### 业内角度换算

在下图中，我们看到了 Angular.js (v1)的缩放目标。这是为了快速构建中小型应用程序。

[![](img/f52ece903cd45e757b32124a8fffcfde.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FJ0YgmCE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/scaling-angularjs.png)

然而，由于技术限制，它无法扩展到更远，这是 Angular (2+)开始的主要原因。我们的目标是拥有 AngularJS +所能达到的 Angular 2+所能带来的一切。相反，现在的情况是，目前最适合企业用例:

[![](img/06aedc03f1db338717ca20b843af2e24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IJjrvEFe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/scaling-current-angular.png)

有了 Angular Ivy，特别是 [Angular Elements](https://dev.to/blog/2019/04/intro-to-angular-elements/) ，这也可以扩展到小型应用程序，在这些应用程序中不需要成熟的应用程序，只需要小型集成点和组件。

[![](img/a477fc34287e5b9a93dc4e8795907a48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X9di0Acs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/scaling-angular-ivy.png)

一个名为“光子”的新项目瞄准了曲线的上半部分，即真正大型的企业应用所在的地方。

[![](img/d01a1c46ebaafbd8c008b44062caf9f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Isl4eDIx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/scaling-angular-photon.png)

正如你所注意到的，在非常高的部分仍然有一个空间(十亿用户应用程序)，虽然该团队还不能宣布任何事情，但他们正在与谷歌内部的一个团队合作，专门设计这些应用程序，以评估 Angular 是否可以扩展到这个空间。

### 棱角分明

众所周知，Angular 完全是在 GitHub 上开发的 OSS，并被引入 Google。谷歌实际上运行在**开发头**(听起来很疯狂)。但与此同时，通过不断地将 GitHub over Google 的 CI 中的 Angular 集成到 Google monorepo 中，数以千计的自动化测试被触发。不仅仅是框架本身，还有运行在 Angular 上的其他应用。这让团队对 Angular 的稳定性更有信心。

此外，谷歌和 OSS 社区之间有生动的交流和互利。

[![](img/5b32ec2976803ab1ac15012141e8ff97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cIMeTyLI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/angular-oss.png)

像 Ivy 这样的项目是从 OSS 社区进入 Google 的，现在已经产生了重大影响。类似的，像 Bazel 这样的构建工具始于 Google，现在正在开源的过程中。

### 棱角分明的合作者

Angular 生态系统不断发展壮大，越来越多的 PRs 来自 OSS 贡献者。为了确保团队不会不知所措，但仍然帮助那些 pr 成功着陆，它需要找到一个扩大这些努力的策略。于是，**角合作者**就此展开。他们基本上是在“层级”的下面一部分(多难听的词)。

[![](img/6e0e70d03098aae8ceb77944a938d57c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K6asLEBb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/angular-collaborators.png)

目标是拥有一组可信的合作者，他们从实际的 Angular 团队成员那里获得指导，被邀请参加会议等，然后可以帮助其他 OSS 贡献者完成他们的 PRs。

[![](img/9a9fbc1117822cb54404eceaf9405cf1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GTxuVDXT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/angular-collaborators-goals.png)

怎么加入？

*   **社区:**成为一名出色的贡献者
*   企业: devrel [at] angular.io

最后...

[![](img/93367e4efbdd7d9a978182d6bdbc6bc5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S3l0aNia--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/empathy-respect.png)

## 棱角分明为企业

*【Stephen fluin】[YouTube](https://youtu.be/4d1HYKL2tt4)*

斯蒂芬这次谈话的目标是减少信息不对称。尽管官方 Angular 网站上有相当广泛的[文档，但某些问题总是作为核心挑战再次出现。此外，我们每个开发 Angular 应用程序的人都有不同的解决方案和方法。所以让我们分享它们，写博客，创建开源软件库。](https://angular.io)

[![](img/b07ebbee7312901d23d1f3d9cc1819c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VXZ65xCV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ng-enterprise-information-asymmetry.png)

### 手机应用程序

首先“你为什么需要一个应用程序？”。目标是什么？回答这些问题也可能影响你选择的技术。目前 Angular 提供了这些方法:PWAs、Ionic 或 go even native with NativeScript。

[![](img/1e99769ee6c6dca122be929ecda3990f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dLARzZSm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ng-enterprise-mobileapps.png)

### 共享组件和开源

根据 Stephen 的说法，目前组织内有这些不同的组件层:

[![](img/e4b1c76e147eb0200617752f8615f008.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SrMSRVn0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ng-enterprise-componentlayers.png)

有构成公司设计系统的 UI 级组件。我完全同意斯蒂芬的观点。这些都是自然出现的。即使您可能不是有意这样做，设计系统可能会出现，以标准化您的应用程序的外观和感觉。另一方面，业务组件是封装了整个通用业务功能的组件，也可以跨应用程序重用。

在开发这些的时候，不要忘记[角形 CDK](https://material.angular.io/cdk/categories) ，它是一个设计系统不可知组件的集合，可以作为你的基础。

在构建共享组件集时，**促进协作**是关键。最好的方法是开源(如果可能的话)。一个很好的例子是 [VMWare 的 Clarity 设计系统](https://clarity.design/)，完全按照 OSS 模型构建。

还可以考虑使用单边回购的方式，这样可以让你快速行动，看到变化带来的影响。它还有助于开发工具来验证传入的 PRs 和其他东西。

### 混合应用&混合环境

在大型组织中，你有不同的团队，不一定都使用 Angular。尽管如此，您可能希望有一套通用的工具，并在 monorepo 环境中组织您的代码。Nrwl 的 NX 正是允许这样做。您可以在 NX 工具支持的单个 monorepo 中开发 Angular 应用程序、类型脚本库，甚至 React 应用程序和服务器端节点，NX 工具是基于 Angular CLI 构建的一组自定义扩展。

[![](img/38f5812b72a86cda5b9c5b0b9ec920c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aRVrrJTZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ng-enterprise-nx.png)

在这种情况下，不要忘记的另一件事是**角度元素**。它允许将角度组件编译成标准的“web 组件”，更具体地说是定制元素。如果你想了解更多，请查看[我关于介绍角元素的文章](https://dev.to/blog/2019/04/intro-to-angular-elements/)。

[![](img/a282f1c6f6a713d7ac883a37239745f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2ZGddBRV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ng-enterprise-ngelements.png)

### 测试

测试——尽管经常被低估——是极其重要的。尤其是如果你走 monorepo 路线，并希望在你的代码库中保持高水平的敏捷性。Angular 团队正式支持 Karma 和量角器，并有一些未来改进它们的计划。

[![](img/305039b0889f8ea3b90f4bc9b811a3ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kBjKWfMq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ng-enterprise-testing.png)

就我个人而言，我是 Cypress 的忠实粉丝，并且已经在多个 Angular 项目中成功地使用了它，尤其是对于集成级别更高的测试。我仍然要调查 Jest，它看起来也很有希望，我听到了一些好消息。

[![](img/33804320382e9dbf0c2a43f209abb125.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KP4wxYXi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ng-enterprise-testing-othertools.png)

信息是:“也可以使用它们”，即使他们没有像其他工具一样被团队正式推广。请随意选择最符合您需求的产品。

你更喜欢截图和视觉测试吗？那么这些可能是好的候选人:

[![](img/f561574188c80123be336630cd7af635.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UEE4f9DD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ng-enterprise-visualtesting.png)

### 渐进式网络应用(PWA)

你需要吗？很有可能。它有助于构建更快、更具弹性的用户体验，并允许与用户进行更深入的互动(通过推送通知、将其安装在主屏幕上等方式)...).Angular 试图让这变得非常容易。给你举个例子。为了增加服务人员支持，您需要做的就是从您的 Angular CLI 调用以下命令:

```
$ ng add @angular/pwa --project *project-name* 
```

Enter fullscreen mode Exit fullscreen mode

如果你仍然怀疑服务人员和 pwa 是否真的被使用？在你的浏览器上进入这个网站:`chrome://serviceworker-internals/`。它为您提供了安装在您的机器上的所有服务人员的列表。是的，它不再是一项小众技术。

### A/B 测试和实验

[![](img/8f9c81ccab568844bcbcf3ce4956496e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9u_e2xfQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ng-enterprise-featureflags.png)

如果您想实现对包大小没有影响的特性标志，您需要在惰性级别(模块、组件)进行拆分，并相应地将它们加载进来。请参阅我关于延迟加载组件的文章，了解更多详细信息:

[惰性负载角分量](https://juristr.com/blog/2019/04/state-lazy-loading-components-angular/)

### 保持最新

[![](img/99918849ac5f53a26477123aa2123064.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x8em_V0J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ng-enterprise-keep-up-to-date.png)

一件事是通过 Angular CLI 命令(`ng update`)自动升级，这些命令在最新版本中运行得非常好。然而，另一个问题是如何让组织中的其他人保持更新的动力。一些策略是

*   通过共享组件推动升级
*   关于语义版本化的教育
*   拉出故事(比如来自荷航的

我个人对更新故事的看法是:努力跟上它。如上所述，`ng update`真的很有帮助。保持最新不仅给你带来了与社区、新库、性能改进、安全缺陷等保持同步的好处。而且，这比不升级然后升级多个版本要便宜得多，升级可能会变得困难/不可能。

### 构建时间

缩短构建时间至关重要，因为它直接影响开发人员的工作效率。如前所述，Ivy 不仅在内存减少方面有所改进，还提供了更好的增量构建支持。TypeScript 也[最近宣布支持更好的增量构建](https://devblogs.microsoft.com/typescript/announcing-typescript-3-4-rc/)，所以你看到的主题是🔥。

升级到 Ivy 已经有所帮助了。从长远来看，巴泽尔将是解决方案。

[![](img/b198fce1ae349a25204ac639201b7642.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yBi-H1hV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ng-enterprise-bazel.png)

Bazel 是按比例构建的，可以选择加入 Ivy，在幕后使用 Bazel(阅读本文中的详细信息)。

### 分享你的问题&解决方案

[![](img/971c549dd2c23466522a23c3a8815940.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wtRLWsTb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/ng-enterprise-share.png)

绝对的！！！这是我[写博客](https://juristr.com/blog)、[在 Egghead](https://egghead.io/instructors/juri-strumpflohner) 和 [Youtube](https://www.youtube.com/user/kitodzt) 上创建课程的主要原因。走出去，成为一名积极的社区成员。

不想建立自己的博客，还是你根本没有时间？前往 [dev.to](https://dev.to) ，这非常简单，几分钟内您就可以设置好😃。