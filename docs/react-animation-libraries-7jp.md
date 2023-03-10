# React 动画库

> 原文：<https://dev.to/areknawo/react-animation-libraries-7jp>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

React 最近在受欢迎程度和开发者满意度方面都创下了记录。它提供的**反应式**和**声明式**方式让开发变得更加简单和愉快。这一切在使用 **JSX** 时更加明显。😉它不仅是在 JavaScript 代码中表达 HTML 的好方法，也是以更具声明性的方式表达许多其他结构和功能的好方法。

有了更好的工具，用户对网站的体验也有了更高的要求。网页设计已经发展到令人印象深刻的水平，其中一个最重要的方面就是动画。🤯当用户做出任何动作时，他们都希望得到**视觉反馈**。这一事实使得注重细节的小动画真正受到所有用户的喜爱。那么，结论是什么？你应该在你的设计中加入动画，即使是小的。有了 React，就不会那么难了。在本文中，我想向您介绍一些最好的动画库和工具——包括通用的和 React 专用的。**尽情享受！** ⚡

[![group of people sitting near round brown wooden table](img/bbf46d307b52c587b2082f13463529c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H96ZaBgU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1535209039648-9524289505fd%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

# 怎么回事？

在我们跳到这个列表之前，让我们先来看看为什么选择一个好的动画库是重要的。我应该说，如果你不打算定制任何动画，只是想给你的页面增加一点动感，那么也许 **CSS 动画**和一些 CSS 类管理对你来说就足够了。但是，即使事实如此，你也可以自由地阅读这篇文章直到最后，因为也有适合你的东西！🌟

现在，回到主题，在 React 中制作动画可能会很棘手。为什么？主要是因为 React 如此受欢迎的一个事实来自- **虚拟 DOM** 。这是因为在你的动画和它们应该动画的东西之间——**真实的 DOM**—通过它的渲染器和基于状态的更新系统保持反应。你不能直接控制 React 渲染器做什么，因此你的动画也没有这样的控制。这就是为什么你不得不相信 React 和它处理某些事情的方式。这并不特别意味着你的动画受到任何形式的限制- **不！**这只是一件需要注意的小事。

更重要的是这个事实本身会带来什么。事实上，React 特定的库可能比其他任何通用库更适合您的 React 工作流。当然，也就是说，我们将涵盖这两个类别，让您有更多的选择！🎉

# 通用

## [【gsp】](https://greensock.com/gsap)

[![img](img/b89f39977946dc9abfa4a4bf8a71db6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X3AGML0o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/02/Screenshot-from-2019-02-18-20-14-57.png)

如果你已经在网络动画领域呆了一段时间，你肯定知道 **GSAP** 。作为动画的**瑞士军刀**，GSAP 是由 [**格林斯托克**](https://greensock.com/) 开发的超高性能、专业的动画工具集。GSAP 本身是多个小工具和插件的集合，例如 [**TweenMax**](https://greensock.com/tweenmax) 、 [**TimelineMax**](https://greensock.com/timelinemax) 、ease 插件等。由于其庞大的社区和许多大公司在使用它，它真的很稳定，坚如磐石。文档、API 和社区对 GSAP 的支持令人惊叹。😉遗憾的是，GSAP 有自己的许可证，在某些情况下需要你支付许可费。另一方面，它使这个项目更加面向未来，维护者更有可能加入。

## [动漫. js](https://animejs.com/)

[![img](img/4c2363c8459b436fcd6eb447680f882d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E0rs2d3j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/02/Screenshot-from-2019-02-18-20-17-52.png)

Anime.js 是一个轻量级的动画库，具有简单而有趣的 API。最初由 [**朱利安·卡尼尔**](https://juliangarnier.com/) 编写，这个库即使拥有**麻省理工学院许可**也已经被证明足够好，甚至比 GSAP 更好！它内置了对各种动画 **CSS 属性**、 **DOM 属性**、**SVG**和原生 **JS 对象**的支持。它的 API 格式良好，易于使用。随着它的社区仍在增长，可以说这个项目正在上升。它的**文档**和登陆页面只是进一步证明了这一点。而有了新的 **v3.0** 库，就没什么好说的了，除了太棒了！🚀

## [姿势](https://popmotion.io/pose/)

[![img](img/488a4b7c6e7f23ed22f0ccc35f75688c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Az-JM5Mb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/02/Screenshot-from-2019-02-18-20-19-47.png)

**Pose** 是 [**Popmotion**](https://popmotion.io/) 的 JS 库之一，用于使用户界面设计的整个过程更加愉快。Pose 本身和上面的两个库有点不同。为什么？嗯，主要是因为它对 **React** 、 **React Native** 、 **Vue** 和 **vanilla JS** 都有实现！这里有很多选择。除此之外，Pose 还有令人印象深刻的**文档**和包含大量示例的登陆页面。整个库的目标是在 JS 的帮助下提供一种声明性的方式来控制 CSS 动画。而且看起来它完成的相当不错！至于这个社区，它比 Anime.js 的规模要小一点，但是发展很快。所以，这可能也值得你去看看！👀

# 特定反应

## [反应-运动](https://github.com/chenglou/react-motion)

[![img](img/e6c79a2e74951c92498a67c40d68f6bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WVBCsv_B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/02/Screenshot-from-2019-02-18-20-21-22.png)

**React-motion** 可能是最流行的 React 专用动画库。它的特别之处在于它是基于**真实物理**。😮这意味着你不使用缓和曲线，持续时间等。就像在其他动画库中一样。相反，您可以设置像**嗅探**和**转储**这样的值，让引擎来处理剩下的事情。这是一个有趣的想法，可能会大大简化动画的创建以及库的 API 本身。当然，这种解决方案也有它的缺点。主要是性能和不同的动画设计方法，不是每个人都喜欢。回到 pros，project 它坚如磐石，有相当好的**基于 GitHub 的文档**和**大社区**支持。自然，与 React 的集成水平也很高。

## [做出反应——弹簧](https://www.react-spring.io/)

[![img](img/349d9f22a59f6eb049a39524c41da022.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YS1vdUcM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/02/Screenshot-from-2019-02-18-20-23-06.png)

受 React-motion 的启发， **React-spring** 提供了一种非常相似的动画设计方法。它的设计基于**弹簧物理学**(这就是名字的来源)，并试图解决 React-motion 的一个缺点——**性能**。因此，尽管如此，作为一个年轻的项目，React-spring 提供了优秀且易于使用的声明式 API，提供了出色的性能和总体的高质量。这就是为什么它的**社区**发展如此之快！😲我个人可以说，这个项目确实给我留下了深刻的印象！另外，如果你喜欢 React 的新钩子，React-spring 也会为你提供一些特别的东西！😉自然， **docs** 牛逼又有见地。我可以说，这个项目绝对值得至少一试！

## [反应-转变-群体](https://reactcommunity.org/react-transition-group/)

[![img](img/a7611e9e5259b64a4cf26efbac29fe04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N6M8gXBi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/02/Screenshot-from-2019-02-18-20-24-31.png)

**React-transition-group** 是一个**官方 React 社区插件**，用于创建动画。它非常小，提供了**低级 API** 来控制组件的状态并制作动画。它还内置了对 **CSS 转换**的支持。文档提供了关于所有可访问功能的足够信息。由于这是一个相当低级的项目，如果你想快速简单地制作一些动画，这可能不适合你。但是，请记住，许多 React 动画库都是基于这个特定工具或受其启发的。这就是为什么它可能是值得知道的，只是为了知道某些事情是如何工作的。👍

## [React-动漫](https://alain.xyz/libraries/react-anime)

[![img](img/a89f533e848c377380353d86cba7b325.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3WMHM5_W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/02/Screenshot-from-2019-02-18-20-26-11.png)

**React-anime** 顾名思义就是**React-specific****wrapper**📦 **for Anime.js** 库。这正是你应该期待的。所有 Anime.js 的功能都被打包到它们的**声明形式**中。对于那些喜欢 Anime.js 或者不太欣赏基于物理的动画的人来说，这可能是一个完美的解决方案。**文档**检查包装器的每个细节，从另一方面来说，包装器不是很**设计良好**。社区在这里不应该是一个大问题，因为一切都是基于 Anime.js 的。所以，如果你喜欢这个具有更多声明性 API 的特定库，这可能是你要走的路。

# 其他情况

## [反应-动画](https://github.com/FormidableLabs/react-animations)

[![img](img/bcb4b4e1ed40b9865dde69cb64592964.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KFDEGCT3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/02/Screenshot-from-2019-02-18-20-28-14.png)

假设你不想为你的网站创建任何高级动画，甚至更好...你根本不想这么做！😉良好的...这就是 react-动画由 [**强大的实验室**](https://formidable.com/) 发挥作用的地方！这是一个围绕 [**animate.css**](https://daneden.github.io/animate.css/) 的简单声明式 React 包装器——简单的 **CSS 动画效果**的受欢迎和尊重的集合。使用 react-animations，您可以简单地交换您选择的动画，并按照您想要的方式使用它。当你使用其他 FL 库，如 [**镭**](https://github.com/FormidableLabs/radium) 或 [**阿芙罗狄蒂**](https://github.com/Khan/aphrodite) 时，整个过程变得更加愉快！如果这就是你想要的，那就不要犹豫，自由使用吧！

## [Bodymovin](http://airbnb.io/lottie/)

[![img](img/f36eae3efbc4d7347e0b434023988af9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p-lFNRVG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/02/Screenshot-from-2019-02-18-20-04-27.png)

再来看看另一种极端情况，当你想为你的登陆页面或类似的东西制作非常复杂的动画时，你会怎么做？嗯，你最有可能想在一些高级软件中做这些，比如像 **Adobe After Effects** 。如果你是这种情况，那么**洛蒂**和 **Bodymovin 渲染器** by [**Airbnb**](https://airbnb.io/) 已经覆盖你了。这些工具允许你在 **web** 、 **React Native** 、 **Android** 、 **iOS** 和 **MacOS** 上运行你的 **JSON 导出的** AE 动画！🤯这给你的动画带来了全新的可能性！我可以肯定地说，这是一个有趣的想法。😂

# 动画你的世界！

所以，这些只是我个人的选择。我希望这篇文章至少能启发你，或者更好地帮助你让你的动画看起来更好，更容易开发。现在轮到你了，选择你的工具**做一些动画**！✌

一定要在评论里写下**你最喜欢的 React 动画库是什么。此外，请务必**分享这篇文章**让更多人看到，并 [**查看我的个人博客**](https://areknawo.com) 了解更多最新内容。一如既往，请务必在我的推特**和我的脸书主页**上关注我，了解更多 **JS 精彩内容**。😎******

 **# 资源

*   [**css-tricks.com**](https://css-tricks.com/comparison-animation-technologies/)*动画技术比较*
**   [**对比 React 应用中使用的顶级 JS 动画库【logrocket.com*的***T3](https://logrocket.com/blog/comparing-the-top-js-animation-libraries-for-use-in-react-apps-2)*   [**React 动画附加组件**](https://reactjs.org/docs/animation.html) 来自【reactjs.org】的**   [**20 有用的 React 动画库【bashooka.com】**](https://bashooka.com/coding/20-useful-react-animation-libraries/)来自****