# 为什么您应该关心您的 Npm 安装

> 原文：<https://dev.to/jsguru_io/why-you-should-care-what-you-npminstall-267c>

好像我们还没有从 11 月 26 日的惨败中吸取任何教训，Javascript 世界再次被撼动。

* * *

一个有超过 200 万安装的流行的 Npm 库有一个后门。等等，什么？！是的，你没听错 *[事件流](https://github.com/dominictarr/event-stream/issues/116)* 库当时没有存档，从你的车库脚本 kiddie 到企业系统到处都在使用，它感染了一个令人讨厌的后门，一个加密矿工/窃取者/什么的。从现在起，我们将称它为 C -病毒的戏剧性效果。

> 该死的秘密炒作，是泡沫破灭的时候了，让我们继续我们的生活。- [我](https://twitter.com/blackcat_dev)，2018 年 12 月

但是，这是怎么发生的呢？嗯，不幸的环境和作者对人的信任的结合导致了我们今天的处境。作者很久以前就停止使用和维护这个库了。由于它没有随着时间的推移而归档，它成为了许多项目的依赖，你瞧，有一天一个好心人溜进了他的收件箱，主动提出自己承担维护图书馆的责任，并继续传递火炬，但他并不好，不是吗？

> ![unknown tweet media content](img/385eb56e0031ab959e0f76c86a0231a3.png)![Felix Krause profile image](img/be3ff90fc0a725a403b2d24314a69da9.png)Felix Krause@ Krause FX![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)步骤 1️⃣浏览最受欢迎的非活动开源库
> 步骤 2️⃣联系作者并请求帮助
> 步骤 3️⃣获得推送访问权限并发布折衷版本
> 步骤 4️⃣一周内达到 200 万应用
> 
> [github.com/dominictarr/ev…](https://t.co/T4CmEJrUmN)18:33

作者将 Npm 模块的权利转让给了这位善良的陌生人，但将模块的代码留在了他的 Github 帐户上，因为当他试图将它转让给陌生人的帐户时，出现了一些名称冲突的问题，或者他是这么说的，但我想这也不是偶然的。这个撒玛利亚人是一个邪恶的阴谋家。
一旦他得到了这个库，他就取消了老作者的出版权，添加了包含 C-病毒的`flatmap-stream`模块，做了一个小版本的改动，最后在 Npm 上发布了一个新版本。
全世界的事件流库都更新到了新的次要版本。
一旦他植入了 c 病毒，他就从库中移除了`flatmap-stream`模块，然后他做了一个主要的版本升级，并再次在 Npm 上发布了一个新版本。很聪明不是吗？现在代码库中没有任何可疑之处，但由于这是一个主要版本更新，大多数系统不会更新到它，因为他们被锁定到以前的主要版本，因此他们仍然有受感染的代码。甚至我的一个同事也被感染了，几乎没人注意到，除了凯文·博蒙特。

> ![🎅🏿 Kevin Beaumont 🎅🏽 profile image](img/504ca99298852f3c7ed26cf05ef41b44.png)T3】🎅🏿凯文·博蒙特🎅🏽@ gossithedog![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)NPM 的 2m 装库有后门，看起来是某种木马(stealer？)[github.com/dominictarr/ev…](https://t.co/BWuhObvTAL)2018 年 11 月 26 日下午 18:39![Twitter reply action](img/269095962147c28351274afdd5486a48.png)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1067125660014051330)364[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1067125660014051330)498

这是一个简短的总结，我可能错过了一两个步骤，但你得到了图片。

* * *

我们不应该责怪作者，他可能真的没有考虑那么多，但是维护开源软件是一件非常麻烦的事情，并且会消耗作者的精神，尤其是在今天这个人们期望一切都是免费的社会。所以我们不要责怪他。

* * *

现在让我们回到正轨！哦，这是凯文·博蒙特的另一颗宝石

> ![🎅🏿 Kevin Beaumont 🎅🏽 profile image](img/504ca99298852f3c7ed26cf05ef41b44.png)T3】🎅🏿凯文·博蒙特🎅🏽@ gossithedog![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)过去几年涌现出的一件事是 npm 和 Node.js 生态系统——价值数十亿美元的公司和初创公司都在构建复杂的系统，这些系统基于由随机的人无偿编写的通常无人维护的代码。2018 年 11 月 26 日下午 18:57[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1067130326395351040)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1067130326395351040)129[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1067130326395351040)327

哈利路亚，赞美上帝！说教！人们不是无缘无故想出这个模因的:
[![Npm modules](img/1c22870aeb9ff64fa2ef9fe62034ceec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XN4Y7QfL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AxMYsLPXMT02nL83x3Qbp6w.png)

**从这场混乱中得到的关键启示:**

*   有时我们认为理所当然，并不认为我们 Npm 安装/作曲要求/纱线添加
*   开发者变懒了。我们已经开始采取“不要太字面地重新发明轮子”，而不是写一些额外的行，我们宁愿需要一个库，有时甚至是最琐碎的任务(特别是在 Javascript 世界这似乎是趋势)
*   你应该总是仔细考虑你所需要的依赖，更多的依赖有时意味着更多的技术债务
*   添加一个抽象层，并设计一个接口作为域逻辑和库之间的桥梁/适配器，这样，如果需要的话，您可以更容易地交换它们
*   当一个库停止维护时，我们现在拥有该代码，我们有责任将其作为我们代码库的一部分进行修复，或者为其找到合适的替代品

今年 11 月，许多企业再次艰难地吸取了这些教训。

感谢阅读我的长篇大论！

* * *

### 出发前…

如果你喜欢读这篇文章，请分享它。你应该看看我们的其他出版物，你可能也会喜欢它们！我们不时地写一些关于软件开发、技巧和诀窍，以及如何成为一个更好的开发人员和商业人士的文章。加入我们不断改进的旅程吧！

在[脸书](https://www.facebook.com/jsguruio/)、[推特](https://twitter.com/jsguru_software)、 [LinkedIn](https://www.linkedin.com/company/jsguru) 、 [Medium](https://medium.com/jsguru) 或 [DEV.to](https://dev.to/jsguru_io) 上关注我们。

* * *

最初发布于 [jsguru.io](https://jsguru.io/blog/why-you-should-care-what-you-npm-install) 。