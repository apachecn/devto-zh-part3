# 十个 AWS 服务如果它们是神奇宝贝(Amázmons？)

> 原文：<https://dev.to/exampro/ten-aws-services-if-they-were-pokemon-amazmons-16hj>

**TL；博士**😪唉，只要向下滚动就能看到一些很酷的插图

AWS 每年都有新的云计算服务，他们有超过 150 项服务。你还能想到什么东西超过 150 英镑吗？🙃

我敦促初学者学习云计算服务的原因是，许多 10 年前可以在单个服务器上构建到网络应用程序中的功能现在正在使用现有的云服务，因此他们的应用程序可以扩展到数百万，如果不是数十万次请求的话。

所以，我要对你们这些即将成为全栈开发者的大三学生说，开发一个应用是不够的，你需要云计算知识，尤其是如果你想让自己的简历脱颖而出的话。

但是给你一个利用云服务的例子，DEV.to 使用 Angolia 进行搜索，Pusher 用于实时事件，Fastly 用于 CDN。

AWS 是云服务的领头羊，大约 60%的初创公司都在使用 AWS。初创公司大量使用 AWS 的原因是，AWS 拥有一切，当你购买整个 AWS 生态系统时，他们服务之间的⚡synergies ⚡超过了使用单一第三方服务，无论单一第三方服务功能有多酷。这就像当你在电子游戏中收集到一套完整的盔甲时，你会得到疯狂的额外技能点。

其他云提供商有漂亮的 GUI，但是 AWS 有最重要的支持和文档。

因此，有多种 AWS 认证，最入门级的认证是认证云从业者。通过考试的一个重要因素是，你需要知道许多 AWS 服务是做什么的，而不是如何使用它们，但你可以在一系列服务中说出它们的名字。

所以我认为学习或者仅仅是了解 AWS 服务的一个有趣的方法就是把 AWS 服务变成神奇宝贝。或许可以称之为 Amázmons。试着大声说出来，这很难。

因此，我已经有 40 个 AWS 服务变成了 Amázmons，目前正在制作一个 Amázmons 模仿说唱视频(顺便说一下，[订阅我的 YouTube](https://www.youtube.com/channel/UC2EsmbKnDNE7y1N3nZYCuGw) ,这样当我放下它的时候你就不会错过了)

费了九牛二虎之力，这里有 10 个 AWS 服务被画成神奇宝贝！

### X 射线

有了无服务器和微服务架构，模块化我们的代码变得越来越容易，因此我们可以混合和匹配语言和功能，权衡我们有更多的移动部件传递数据，很难找出哪里出了问题。这就是 X 射线解决的问题，它可以追踪所有这些互联服务之间何时何地发生故障。

[![](img/9d3afb5b20f481e9d7000678554605eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z9LKkI7e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r39vbb9syqeagt4sv42g.png)

### S3

S3 是简单存储服务的缩写，通常是人们开始使用的第一个 AWS 服务。这是一个存储文件的地方，但也可以用来托管静态网站，如果你认为你已经可以在 Github 上这样做，我会坚持我的观点，S3 更适合静态网站托管。如果你曾经使用过 Dropbox，它实际上只是在他们的 GUI 下使用 S3。

[![](img/257170e1c9b5172ee66a4849ebfd81ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WIQqh-JL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qul72qv0s30ei753zm3g.png)

### 你们有吗

Macie 不断查看内容和访问您的 S3 存储桶的用户，以确定是否有任何奇怪的事情发生。一个很酷的用例假设你在 S3 上存储了信用卡信息，但你忘了加密，Macie 会注意到这一点并警告你这个漏洞。

[![](img/1623020463feebab7cd234c171baa44f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ToqXXnJN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/buoha6nna3n5u575zvqz.png)

### λ

AWS Lambda 是无服务器功能。因此，如果你想运行一段只需要运行几秒钟的代码，但又不想麻烦地配置服务器，那么这是 Lambda 的一个很好的用例。例如，在 ExamPro 上，我们的联系人表单由 Lambda 函数支持，这允许我们将不是核心 web 应用程序一部分的代码保留在主要代码库之外。

[![](img/9ab37c45705d63886bbd485bdd6f33d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aeX6BWm5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pc0jb28mgctav3j3xy38.png)

### 亚马逊索赔

假设您有成千上万的视频片段，您需要读取汽车牌照。Rekognition 可以为您做到这一点。我在这里加入了 Rekognition，因为老实说，我觉得这个插图看起来真的很酷。

[![](img/fc067a187f3c2f2ceb975244c6bc56e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tpltHEca--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/voglhd3o67ytzko6gqsq.png)

### 雅典娜

Athena 允许您将 JSON 或 CSV 格式的 s3 文件转换成可以查询的内存表。如果你查一下 Apache Presto，那就是 Athena。因此，Athena 的一个用例是，您可以使用它来检测您的 AWS 帐户中的高级活动。另一个默认打开的 AWS 服务叫做 CloudTrail，它监控你的帐户中来自 AWS 服务的所有 API 调用。由于 CloudTrail 可以将其 JSON 日志输出到 S3，因此您可以使用 Athena 将这些日志转换成表格，并创建一个查询来检测(比如说)何时有人在奇怪的时间从您的网络外部访问服务。明白我说的⚡synergies ⚡是什么意思了吗

[![](img/5e22f20f31164ad9bbb5066d5b124976.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c7DaXGWY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/62s9w0ssw9158gpajnsh.png)

### 海王星

Neptune 是一个受管理的图形数据库。因此，如果你听说过或查找 Neo4j，它就像那种数据库。我只有过一个图形数据库的用例，它存储了组成食物食谱的配料之间的关系。

[![](img/8761a149a98297f7b285ba7ef2ca305c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7u4qvRKt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rr9300exy9hk4hcerisq.png)

### 红移

红移是当你有很多很多的数据，你想存储它，并分析它以后。我们谈论的是数十亿字节的数据。红移是一种列存储，这意味着数据存储在列中😋咄！这就是红移可以很快查找数据的原因。

[![](img/9dac52555cc39cc7ca0ed03235707bfa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TBo5qpqP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3boaccchh1xyaj75sled.png)

### 代码线

CodePipline 是您的 CI/CD。因此，我们在 ExamPro 中使用 CodePipeline，这样当我们使用 git push 时，它会自动触发我们的 web 应用程序开始部署，我们还设置了它，以便我们有一个测试阶段，只有当我们的测试套件通过时，它才会部署更改。

[![](img/1b0d48868c877f42c3c98764d57d3031.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4E2bu0rl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f3yw3q79tra7mxl3e0kd.png)

### EC2

EC2 是 AWS 的主干，直到他们推出了一种叫做 AWS 主干的服务。EC2 就像数字海洋或 Linode，它是一个服务器，在那里你可以选择你想要的操作系统等等。你能想到的任何 AWS 服务都运行在 EC2 上。所以当你使用 RDS 时，它在 EC2 上运行 Postgres/MySQL 服务。因此，如果您了解 EC2，它会让您深入了解所有 AWS 服务。

[![](img/a899eaec749e774a87f3cdbfa1d858ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6eyKXwJp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gbekye59ywof9d7puqzb.png)

这难道不是了解 AWS 服务的有趣方式吗？