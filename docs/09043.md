# 使用 React 挂钩

> 原文：<https://dev.to/swiip/using-react-hooks-15ll>

<figure>[![](img/3ab8442fe45a602af749e29f245337da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wbdq1fkS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASUMAGSoyzrMkL_h40XFAcg.jpeg) 

<figcaption>照片由[罗布森·初美·摩根](https://unsplash.com/photos/qr7tsSwDOg0?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/fishing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

这是我第一篇帖子 [React Hooks 的后续，我的介绍](https://dev.to/swiip/react-hooks-my-introduction-17ee-temp-slug-2130666)。如果你不知道钩子是什么，或者只是想从头开始，请先看看这篇文章。

正如承诺的那样，我现在已经在一个 PoC 项目上使用了钩子: [pokemon-arena](https://github.com/Swiip/pokemon-arena) 和一个生产项目:zenika.com 的[网站(不是开源的，抱歉，也许有一天……)。我没有很多关于 PoC 的反馈，但是在一个真实的项目中使用 hooks 带来了新的问题。](https://www.zenika.com/)

这篇文章是我使用 hooks 的经验反馈，涉及一些开发者的观点，比如:代码重用，第三方库和测试。

最后提醒一下，React 团队和我都不建议你像我一样重构整个代码库。这只是一个新的选择加入功能，如果你想使用。我积极地做出改变是出于好奇，而不是因为我需要它。

### 代码重用

钩子提供的主要好处之一是能够重用代码。作为 [Recompose](https://github.com/acdlite/recompose) 的用户，我已经能够重用代码作为定制的特设。这是我首先使用它的最重要的原因之一。切换到钩子首先允许我移除库本身，然后将我所有的自定义 hoc 转换为自定义钩子。这非常简单，因为我可以用相同的语义一一替换它们。

虽然代码减少不多，但我很高兴看到在删除一个库的同时我还能保留这些好处。

我们来看一个组件逻辑共享的例子。在 zenika.com，有一份来自 graph QL 请求的代理商名单。我有一点逻辑(与[重叠](https://redux.js.org/))来打乱列表，不要每次都呈现相同的顺序(并且不让任何人嫉妒)😂).但是我在网站的两个地方使用这个列表。

此功能有几个要求。多次使用时只加载一次数据，混洗一次以避免每次渲染时产生不同的结果(避免不一致的渲染)。简单地说，不要忘记每次都使用混洗列表(我提到它是因为我有 bug)。

提取一个定制的钩子来收集获取数据、启动 shuffle、然后返回列表的逻辑很快就有了意义。使用定制挂钩有很多好处:

*   其他开发人员只需考虑使用该挂钩来获取代理列表。他们可以忘记洗牌逻辑。
*   两个不同的组件将总是使用相同的逻辑来访问机构列表，并且不能有不同的行为。
*   它非常适合测试和隔离问题。钩子将能够与任何组件隔离测试，只检查获取代理的逻辑。组件可以假设挂钩中的代理是好的，并关注它们自己的显示逻辑。