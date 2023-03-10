# 编码争议

> 原文：<https://dev.to/areknawo/coding-controversy-485l>

这篇文章会有些不同。我将描述我对我自己的 OSS 库的正面和负面印象，因为这是我脑海中一个非常有趣的心理辩论。所以，记住-你已经被警告过了。😂

# 有点背景

故事从我开始——自学成才的开发人员——希望更好地掌握 JavaScript 语言中的正则表达式。一开始，regexps 对我来说有点像某种魔法。我究竟该如何学习所有这些可能的表达和模式呢？在这个故事发生之前，我只是笨拙地一遍又一遍地查看参考指南，勉强创建了自己非常非常简单的正则表达式。这只是痛苦而已。对于搭建，我使用了 **regexr** 和后来的**regex 101**——用 JS 和其他多种风格创建 regexp 的在线编辑器。现在， **regex101** 支持更多种类的风格和更新的 JS 语法(例如 ES2018 命名组)。另一方面，Regexr 提供了更好、可读性更强的参考指南 IMHO，但只针对 JS 和 PCRE 版本。如果你还没有，考虑看看它们。所以，我开始学习 regexr 及其文档。我慢慢地开始创建自己的正则表达式，但没有什么令人印象深刻的。然后我买了一本关于这个特定主题的书，从头到尾读了一遍。它也没有让我对正则表达式感到很舒服。我对这种情况感到有点厌倦。

[![photo of bulb artwork](img/71dea046d24afddb83498939e3d8193f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xkWN_RoZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1512314889357-e157c22f938d%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ) 摄影:absolute vision/[Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit)

# 想法...甚至更大的背景

我的整个经历让我感到疑惑——如果正则表达式很难理解、阅读和书写，为什么每个人都使用它？我现在知道这不是一种正确的想法，所以在你对我有任何误解之前，请记住，这只是我的全部故事。😉

那时我也在做另一个我自己的副业——介于 app 和扫雷游戏之间的东西。通过这样做，我想试试自己的反应是否像许多人认为的那样好。这里有一个旁注——那时我是 **Vue** 的忠实粉丝，但这是另一个故事了。为了这个项目，我也开始使用 **Redux** 。这样，主要是出于好奇和 Redux 团队对良好编程架构的关注，我试图将 **ImmutableJS** 应用到我的这个项目中。也许，只是也许是受我被“*洗脑*”的影响吧😄在[讲座之后**清洁建筑**由**鲍勃**大叔](https://www.google.pl/search?q=Clean%20Architecture%20Robert%20C.%20Martin)主讲。事实上，我对 **ImmutableJS** 的功能 API 印象深刻，以至于我真的很喜欢使用它。我最喜欢的特性是所提供方法的可链接性。这让编写代码成为一种美妙的体验。尽管它也有缺点。主要的问题是令人失望的对 **TypeScript** (我非常喜欢)的开箱即用支持，因为它使用了自定义的、库提供的数据类型，并且我非常关心性能(记住——这是一个类似游戏的项目)。此外，除了伟大的 API 之外，我没有其他理由使用**immutableejs**，因为它的主要卖点之一——可预测性——已经与 ts 一起提供了。

现在，暂时把这个故事放在一边，我对 JS 中函数式和可链接 API 的潜力印象深刻。这个想法，当与上面的想法结合时，引导我创造了另一个项目...

# 向 ReX.js 问好！

我的第一个 OSS 库就是这样来的。在它的开发过程中，我试图将每个正则表达式的构造看作一个独立的、可链接的函数。我希望为所有可能的组合/正则表达式提供一个函数替代。我还想让雷克斯做些不仅仅是那样的东西。我考虑了可扩展性和目前 JS 风格的 regexps 中没有的特性的聚合填充——比如 **lookbehinds** 。这样一来，完成这项任务就变得困难多了。就这样，经过十几天的开发，我放弃了这个项目。大约两个月后，我又回到了那里。我完成了聚合填料，但仍然-它们并不理想。不管怎样，我决定试一试，并把它发表在 [**GitHub**](https://github.com/areknawo/Rex) 上。然后，我在一些社交平台上分享了一下。它在**黑客新闻**上获得了 **58 分**并引发了一场颇具争议的辩论，从而引起了人们的极大兴趣。最被否定的方面是 **ReX** 的 API。许多人认为它太臃肿、太难/不值得学习。甚至在看完这些[评论](https://news.ycombinator.com/item?id=18488958)之后，我真的不知道该怎么做。我修复了一些尖锐的问题，主要是我不知道如何创建一个好看的回购，徽章和我的一些误读。API 保持不变。我在想人们对此的看法，但从我的角度来看，事情本该如此。当然，API 有点(🙃)臃肿，但所有方法大多以其正则表达式的构造对应物命名，而 **TypeScript** 旨在为每个开发人员提供正确的编码体验。所以，当 **Github stars** 停滞在 **245** 的水平时，我认为这是炒作的结束，也是一个成功——对于第一个 OSS 库来说，245 的起点是一个令人印象深刻的分数，或者至少我是这样认为的。可悲的是， **NPM** 的安装并没有那么好，从大约 **400** 下降到 **10** 。😕

[![close-up photography of United Kingdom dart pin](img/d7fbd854268600b40028e0ea10e16847.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AQrb7i51--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1532685274648-71618a3b66c7%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ) 照片由[raw pixel](https://unsplash.com/@rawpixel?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit)/[Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit)拍摄

# 直奔主题

所以，最近我开始了 **AIM** 项目(新编程语言),如果你感兴趣的话，我已经在这个博客上写了一些关于这个话题[的东西。自然地，为了创建一门语言，你需要做一些文本处理，(关于这一点的文章即将推出)，对于文本处理来说，最好的选择是——很明显——regexp。你可以认为我只是跑去我自己的图书馆，因为为什么不呢？😀嗯，也不完全是那样。🙃是的，我考虑过 **ReX** 但是我开始担心性能和额外的 **4KB** 代码，除了让自己感觉良好之外没有任何其他原因。所以我决定在这个项目中使用雷克斯。多么讽刺，我创建了一个从未真正使用过的库。实际上感觉有点奇怪。](https://areknawo.com/tag/aim)

有趣的部分来了——我对自己图书馆的使用和体验——大部分是不带偏见和批判性的。第一印象很好——它给了我链接和重用匹配表达式的简单能力，但是使用 JS regexp 构造函数和字符串没有什么做不到的。但是，不用说，当把单独定义的语法结构 regexp 合并成一个大的 regexp 时，它确实给了我一种很好的感觉。接下来，我为命名组编写的 polyfill 允许我毫无问题地使用它——这对我帮助很大。可悲的是，这就是职业生涯的终点。至于缺点，只有一个真正的大样板。尤其是在考虑小的一行程序时，当使用 **ReX** 时，就变成了冗长的、连锁的、不可读的一行程序或者看起来不错的 3 或 2 行代码。这让我震惊。当需要做出决定时，考虑到性能和代码长度，我决定使用干净的正则表达式。作为一个旁注，我甚至没有想过使用干净的正则表达式和使用 **ReX** 作为一个没有完整 API 的绑定器，这是完全可能的。这主要是由这次经历伤害了我的感情所驱使的。

# 反思

所以，在这一点上，我认为雷克斯既成功又失败。作为成功，因为它达到了它的目的——我终于对编写正则表达式感到满意了，我甚至为初学者和那些想更新知识的人写了一篇关于它们的[教程](https://areknawo.com/hello-regexp/)。这对我和我与 regexps 的*关系*有很大的影响。现在我真的知道学习的最好方法是通过实践(是的，真正的发现😅).此外，对于我的第一个 OSS 项目，我认为它是成功的，即使它的整个流行可能是由争议驱动的。我为我的创作和创造它的我自己感到骄傲。话虽如此，我已经吸取了教训。作为一个成熟的库来发布一个旨在实验和学习的项目并不是一个好主意。🤔如果你和我一样，想要创建你的第一个 OSS 库，那么确保它很好地服务于它的目的。先做一个解决**自己问题**的库。然后**在*战场*中测试**(不仅仅是测试框架，你也应该这样做)，用它来解决你的问题，然后继续做好文档、回购等。记得在向全世界发布之前做好一切准备。那是我犯的错误，我没有亲身体验过使用它。

[![silhouette of road signage during golden hour](img/ee6709db999f396ce1f7483c463ba33e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ASx_jNMF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1533073526757-2c8ca1df9f1c%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ) 照片由[哈维尔·阿莱格·巴罗斯](https://unsplash.com/@soymeraki?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 拍摄

# 接下来是什么？

很可能是关于创建 lexers 的帖子！😁但真的，我不知道。🙄与雷克斯的经历教会了我很多。当创建下一个 OSS 库的时候，我肯定会让它变得有点不同，有点更好的**。至于**雷克斯**，我有维护它的计划，最有可能改变 polyfills 架构。但是我最有可能不去管 API。改变它没有多大意义。它会破坏其他人的项目(谁知道也许有一些)，让 **ReX** 变得完全不同。另外，请记住，在使用 **TypeScript** 和 **ReX** 的扩展时，您可以删除部分 API。但是，现在，我想我们到此为止了。🎉**

 **# 感谢现在

所以，这篇文章有点不同——更像是我自己的故事。我只希望你喜欢它，觉得有点娱乐。如果你有，那么你可以看看我的[个人博客](https://areknawo.com)了解更多有趣的故事。目前就这些。😉**