# 制造、突破和创造-黑客马拉松版

> 原文：<https://dev.to/pmca/making-breaking-and-creating-hackathon-edition-m8p>

[![AINI Hackathon](img/98f52be953ad2f33ee6aa7a4d050c3ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OBHe2u-D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/703ycyqsjzar6gi47n7n.jpg)

过去的这个周末，克洛伊·麦卡蒂尔、埃蒙·康普顿和我参加了艾尼黑客马拉松，这是北爱尔兰有史以来规模最大的一次！

黑客马拉松在两个地方举行，贝尔法斯特(QUB)和德里(UU)，总共有 200 多人参加。主题是善用人工智能，构建创新的解决方案，造福世界或人类。

* * *

这一切都是在周六开始的，我们早上就到了，没有浪费时间乱搞——我们很快就抓住了一个项目室，因为我们认为这将是让我们进行头脑风暴、白板和让创意流出来的最佳环境。

准备好便利贴和白板后，我们开始致开幕词，开幕词中记录了一些重要的资源，包括第二天将使用的评判标准。这是完全透明的，每个人都有机会给自己最好的竞争机会！

喝了一点咖啡因后，我们感觉很好。头脑风暴开始了。

[![Essential tools for hackers - post-its, snacks and a pensive pose](img/fe4566b25f5a624936dc7c3ee6cf2507.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Eda7lIIt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d5pabklsbm3smrglm1p5.jpg)

* * *

我们希望我们的想法尽可能与主题保持一致，构建一个对人类有积极影响的解决方案。经过一些初步研究，我们发现每个行业都普遍存在的一个问题是，人为偏见(有意或无意)在求职者的初步筛选中起着重要作用——尤其是在简历入围名单中的偏见。

我们发现了一些惊人的统计数据，这些数据让我们明白这确实是一个大问题。我们如何利用我们的技能来解决这个问题？我们能否创造一个可行的解决方案，对人类产生积极影响，并有可能消除这种偏见？这个问题的解决方案有多可行？

这个活动只有一天多一点，但是我们有雄心勃勃的计划。

* * *

## 输入 DiverseCV📄

我们希望打造一种体验，为雇主和潜在候选人创造公平的竞争环境，确保候选人相信他们受到了公平的对待，并有机会凭借自己的技能在招聘过程中取得进步。

对于一个三人团队，我们有相当多的不同技能，从前端开发到机器学习和数据科学。我们决定利用自然语言处理来构建一个引擎，该引擎将处理简历，并删除任何可能构成人类偏见的潜在标志，例如:性别、种族和民族。

然后，我们希望将引擎包装起来，并作为一个简单的 API 公开，以允许不同的客户端进行交互。和大多数 ML/AI 世界一样，Python 的使用量很大——所以我们决定这肯定是阻力最小的途径。满足于将 Django 作为我们的 web 应用程序框架，将 PostgreSQL 作为我们的持久层，我们已经准备好了。

然而，当谈到投球时，我们不想仅仅展示一个 Postman 请求或 cURL 命令——我们想要一些更实际的东西，所以我们决定构建一个简单的 React 应用程序来与我们的 API 进行交互。

[![Chloe giving us a whirlwind on how our NLP engine should work inbehind the scenes](img/d7706f6e557d2aaf7128389da239a702.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4UxYdnWp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1mn78y3pp1a0p77ixir9.jpg)

## 让我们得到工程🛠

对于前端，我们希望展示使用该服务的两类用户的观点——申请人上传他们的简历，公司查看申请人的匿名简历。我们建立了一个简单的界面，允许申请人在发送到后端服务之前上传他们的简历。

UI 的另一面是一个简单的 accordion，显示每个匿名的 cv，其中只有与数据库中存储的提交链接的相关 UUID 指示符。

对于后端，我们只是在 Django 中暴露了几个与数据库交互的端点，但在将文档存储到 S3 桶中之前，关键是调用自然语言处理功能。

到了星期六晚上，我们都筋疲力尽了——这是令人难以置信的高效率，但我们需要休息。合上笔记本电脑，拿起啤酒，我们称之为一个晚上。在设法让两个服务都启动并运行之后，我们对我们的进展很满意。

* * *

## 投币📣

周日早上。是时候介绍我们的想法和进展了。由于评判标准是完全透明的，我们希望确保我们符合每一个标准——我们聚集在我们的项目室，开始通过每一个标准，并为我们的想法制作一些幻灯片。我们真的想把这个问题的严重性以及我们的解决方案的可行性讲清楚。

至关重要的是，在黑客马拉松的最初开幕式上，人们强调评委们希望看到已经构建好的解决方案——而不仅仅是一个有解决方案的有血有肉的想法的问题。

[![Readying our story telling abilities](img/560f202b177fc1669616ddf552940977.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eSb3c9qy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/302kx1hdrgluzvhfhwfl.jpg)

喝了点咖啡后，我们很快将两种服务合并在一起，开始练习我们的推销。

给定一个严格的五分钟时间，我们强调了我们的解决方案的不同方面，包括技术和总体可行性。我们还强调，该解决方案集成到处理招聘的不同现有平台是可行的。

虽然我们用不同的使用案例和技术复杂性来表达这个想法，但我们想强调的是，我们的最终目标是帮助创造公平的竞争环境——让每个人都有公平的机会来评判自己的技能。

## 结论

在黑客马拉松的闭幕式上，我们被授予第三名，真是欣喜若狂！整个周末，人工智能有一些令人难以置信的创新用途，从深入的数据科学和分析到手语的实时翻译。

非常感谢组织者， [@AI_NI_community](https://twitter.com/AI_NI_community) -总的来说，这是一个精彩的周末，非常愉快。期待下一部！