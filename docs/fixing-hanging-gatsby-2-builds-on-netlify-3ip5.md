# 修复悬挂盖茨比 2 建立在 Netlify

> 原文：<https://dev.to/kevinmeyvaert/fixing-hanging-gatsby-2-builds-on-netlify-3ip5>

几年来，我一直在做一个音乐节网站，作为一个宠物项目。去年，我决定使用 [JAM Stack](https://jamstack.org/) 来重建整个系统。[心满意足](https://www.contentful.com/) + [盖茨比](https://www.gatsbyjs.org/) + [Netlify](https://www.netlify.com/) 。它很棒，很快，可扩展，对我来说，完全免费。

几个月前，盖茨比发布了他们的 v2，我渴望更新网站，使用所有新的花哨东西。然而，在升级后不久，当通过 Netlify 进行构建时，构建卡在“缓存节点版本 vx.x.x”步骤，并最终超时。

[![This was no fun](img/05c1645979c504a1dcbbe00d64f0dac1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E-bVX9zM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/779lew7lm5s4s3yyfv2z.png)

我做了大约 40 次提交，对代码做了轻微的修改，恢复了依赖关系，尝试了不同的节点版本，但是没有任何效果。在我浪费了整个周末试图解决这个问题后，我决定停止尝试。

我是那种固执的开发者。我必须自己解决这个问题。🤦🏻‍♂️与此同时，几个月过去了，我真的不得不开始着手 2019 年版网站的工作。就这一次，我不得不呼叫支援骑兵。

最后，我得到了 Netlify support 的 Chris & Dennis 的帮助(大声喊出来，你让我开心)。他们给我指出了几个月前在 Gatsby v2 测试版期间在 Github 上记录的一个问题——他们在那里解决了一个 bug，这个问题仍然在最新版本中。

看起来。失败的构建是由 Gatsby 在构建期间产生的 Jest worker 子级引起的。但是当父 Jest worker 退出时，子 Jest worker 会继续存在，导致 Netlify 上的构建超时。

## 解

TL；DR
*不要在 componentWillMount 上使用 setTimeout。*但是当你这么做的时候，确保你只在运行时执行它。(把它包在一个窗口里！== '未定义'检查)

[![I know I know](img/930a492af2944ad2ee09ce6c42334cc1.png)T2](https://res.cloudinary.com/practicaldev/image/fetch/s--iEMGkbPZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ygpo53660t7kg6puiywg.png)

还有。根本不要使用 componentWillMount。像所有酷孩子一样用钩子。😎🆒

[![Hooks are so cool omgwtfbbq](img/91ad9e6e3a1c11ba745f95662635bd98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fk92A3YK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2kpw9ixuu7zx87paf4ap.png)

反正这个问题让我损失了很多时间，给了我第一根白发。我希望写这篇文章能在不久的将来帮助到某人。

PS:当在 componentWillMount 中使用某些 firebase 或 aws-amplify 函数时也会出现这个问题，因为它们也使用 setTimeouts。