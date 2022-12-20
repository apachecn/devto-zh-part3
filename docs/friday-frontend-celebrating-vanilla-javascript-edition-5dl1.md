# 星期五前端:庆祝香草 JavaScript 版本

> 原文：<https://dev.to/kball/friday-frontend-celebrating-vanilla-javascript-edition-5dl1>

希望你在三月的最后一周过得愉快！我们马上就要进入春天了。本周，我为您准备了一大堆有趣的普通 JavaScript(或 ESNext，即“新的普通”)文章。我特别喜欢用普通代码重新创作 RxJS。

还有一堆很棒的 CSS 文章，几篇关注性能的文章，以及一首简单的颂歌。

尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [CSS 中模糊的边界](https://css-tricks.com/blurred-borders-in-css/)

非常酷的文章，探索如何在图像上创建模糊/磨砂边框。大量使用了`:before`伪元素，以及一些有趣的属性，如`clip-path`和`background-clip`。

##### [关于 CSS 没人教过我的东西。](https://medium.com/@devdevcharlie/things-nobody-ever-taught-me-about-css-5d16be8d5d0e)

一组相对基本但经常被忽视的 CSS 概念。值得一读。如果你做了很多 CSS，你可能只是点点头，说“是的，我知道”，但是如果其中有一个你忽略了，学习它将会非常有帮助。

##### [终于，一个 CSS 唯一的解决办法来了:悬停在触摸屏上](https://blog.usejournal.com/finally-a-css-only-solution-to-hover-on-touchscreens-c498af39c31c)

前几天我还在和这个问题做斗争——在使用触摸设备时处理风格和交互简直是一场噩梦。本文讨论了一个新的规范，应该会有所帮助。

##### [前:后:伪元素的酷用](https://dev.to/kball/cool-uses-of-the-before-and-after-pseudoelements-2l0b)

(偏见警告——我写了这个帖子)。通过使用`:before`和`:after`伪元素，我们可以看到一些令人惊奇的事情。没有深入到超深的深度，更侧重于展示有趣的例子和链接到更深入的文章，为那些想更深入。

##### [屈挠器](https://www.flexulator.com/)

这是一个超级酷的项目，可以用来试验和直观地理解 flexbox 属性。您可以添加和删除元素，操纵它们的每个 flex 设置，并在您的眼前看到事情的调整。非常整洁！

#### JavaScript

##### [JavaScript 规范的主页](https://hacks.mozilla.org/2019/03/a-homepage-for-the-javascript-specification/)

这是早该迈出的一大步！随着 JavaScript 的不断发展，各地的开发人员都在努力跟上发展的步伐，但是却没有很好的核心资源来跟进。有一组 github 回购，以及 TC39 中的人们偶尔写的文章。但是现在有一个集中的网站！这篇文章详细描述了它的想法，这里的[是网站本身](https://tc39.github.io/)。

##### [如何在 JavaScript 中删除数组中的重复项](https://blog.bitsrc.io/how-to-remove-duplicates-from-an-array-in-javascript-b4b2fcf3530e)

很好的文章，介绍了一个常见问题的解决方案——删除重复。当然，您总是可以使用 lodash [uniqBy](https://lodash.com/docs/4.17.11#uniqBy) 实用函数，但是这将向您展示如何自己实现它，并且是一个很好的练习，可以让您更好地使用数据数组。

##### [用普通 JavaScript 介绍 RxJS 概念](https://dev.to/creeland/intro-to-rxjs-concepts-with-vanilla-javascript-4aji)

RxJS 是处理异步性的一种非常酷的方式，在 Angular 社区中已经被广泛接受，但是人们通常很难掌握，因为 API 面很宽。这篇文章很好地介绍了一些 API 表面和相关概念，通过用普通 JavaScript 一点一点地构建它来接近它。

##### [JavaScript 新增#私有类字段](https://jamie.build/javascripts-new-private-class-fields.html)

这是一个期待已久的特性，JavaScript 语言即将在类中添加真正的私有字段。私有字段提案已被合并到一个更广泛的类字段提案中，现在处于 TC39 流程的第 3 阶段，这意味着它“几乎”完成了。伙计们，这是真的！无论如何，本文将向您概述该特性是什么以及它是如何工作的。

##### [如何思考打字稿](https://areknawo.com/how-to-think-and-type-in-typescript/)

作为一个仍然坚持“我应该学习 TypeScript”而不是“我正在使用 TypeScript”的人，我发现这篇文章很有趣，但有时很难理解。我从中受益匪浅，但希望这篇文章为初学者做了更多的解释…而且肯定有一部分让我大喊“这就是 TypeScript 的问题！”-当简单的对象属性循环变得如此复杂时。提醒一下，虽然类型可能非常有价值，但在松散类型的语言中，有些构造工作得很好，但使用类型却很麻烦。

#### 提升链接

##### [完整的 2019 年网络开发训练营仅需 11.99 美元](http://click.linksynergy.com/fs-bin/click?id=hIdOL5Z4eK4&offerid=507388.9948&type=3&subid=0)

这笔大买卖只剩 3 天了！对于那些刚刚开始这个我们称之为 web 开发的奇妙旅程的人来说，这是一个很好的课程，它将让你掌握 HTML、CSS、JavaScript 的基础知识，让你使用 Bootstrap 快速构建前端，并教你如何使用 Node.js 和 MongoDB 构建后端。一个很棒的“盒子里的训练营”正在 Udemy 上出售。

#### 其他牛逼

##### [我们如何建立世界上最快的会议网站](https://2019.jsconf.eu/news/how-we-built-the-fastest-conference-website-in-the-world/)

非常有趣的阅读，看很多你可以做的事情来建立一个闪电般快速的网站。我认为最大的收获是使用静态站点生成器。:)然后在此基础上进一步优化……但这第一步会让你走得更远。

##### [预载、预取等标签](https://3perf.com/blog/link-rels/)

非常有趣的是，你可以查看一些选项，向浏览器建议可以做些什么来加速用户可见的加载新资源的性能。我对看到像`preconnect`这样我以前不知道的东西特别感兴趣。

##### [简单&无聊](https://css-tricks.com/simple-boring/)

对现代前端开发的复杂性感到沮丧是很常见的，然而我们也花了很多时间搬起石头砸自己的脚，故意制造更多的复杂性。为什么？我们与简单的关系是复杂的——我们声称渴望它，但也经常发现它令人厌烦或缺乏动力。这篇文章对我们在 web 开发中思考和谈论简单性的方式进行了有趣的探索。

##### [被谷歌杀死](https://killedbygoogle.com/)

这是一个有趣而悲伤的虚拟墓地，存放着谷歌已经淘汰的所有应用程序。我对此百感交集——对于公司来说，能够取消一些事情并继续前进是很重要的，否则他们会陷入在无意义的事情上浪费资源。另一方面，随着越来越多的产品被巨型企业吸收，有时它们最终会因为缺乏规模而被扼杀，而这些规模本来可以成为伟大的独立企业。直到今天，我仍然怀念谷歌阅读器，我知道我的大量 twitter 订阅都在哀悼收件箱被 Gmail 取代。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】