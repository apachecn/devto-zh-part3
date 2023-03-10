# 制作艾米莉·狄金森诗歌机器人

> 原文：<https://dev.to/nprimak/making-an-emily-dickinson-poetry-bot-4pmk>

昨天我发表了一篇关于我如何使用 Spell 的帖子，这是一个机器学习框架，允许你更快地训练你的模型，而没有对你的机器进行依赖管理的痛苦，来实现风格转移。我继续使用法术，并扩展到探索其他训练模式。

[![nprimak image](img/30410b1242e74f0f13221497509f7baa.png)](/nprimak) [## 我对机器学习的尝试——风格转换和拼写

### nadya Primak Apr 18 ' 193 分钟阅读

#machinelearning #beginners #tutorial #learning](/nprimak/my-foray-into-machine-learning-with-style-transfer-and-spell-1lp1)

今天我想写的是用拼写制作你自己的文本机器人。我一直是艾米莉·狄金森诗歌的超级粉丝。我知道有些人觉得她的作品有点病态，但她的诗歌多年来一直在与我对话，我一直惊叹于一个很少离开家的人如何能够对人类状况、自然世界以及生与死的现实有如此难以置信的洞察力。

因为 Spell 方便地提供了一个视频教程来创建一个根据《毁灭战士》中的歌词训练的机器人，所以我想我可以很容易地将它应用到艾米莉·狄金森的诗歌中。

令我惊讶的是，最难的部分不是培训，而是网页抓取部分。我从 Node.js 的网页抓取教程开始([https://medium . freecodecamp . org/the-ultimate-guide-to-web-scraping-with-node-js-DAA 2027 DCD 3](https://medium.freecodecamp.org/the-ultimate-guide-to-web-scraping-with-node-js-daa2027dcd3))，然后翻阅了一些 puppeteer 和 cheerio 的文档，想知道如何让抓取器在到达页面结果的末尾时按下“下一步”按钮。

起初，我决定刮掉《poets.org 》,它只有 5 页艾米莉·狄金森的诗。这似乎是微不足道的数量，我知道艾米莉·狄金森一直非常多产，所以我做了一些网上搜索，以找到一个更好的来源。虽然有点难刮，poemhunter.com 最终成为一个更好的来源与 31 页的艾米莉狄金森的诗歌。

在这一点上，我确实遇到了一些问题，出现了许多关于套接字挂起的错误，我认为这些错误会导致刮刀崩溃。我无法准确推断出原因，但是通过修改我的文件写入函数，将每首诗逐个添加到文件中，而不是将每首诗附加到一个越来越长的字符串，并在最后写入文件，我仍然得到了我所期望的数据。

艾米莉·狄金森的所有诗歌都被添加到同一个文本文件中，以便于 LSTM 训练模型处理数据。一旦创建了模型，我就将其下载回我的机器，并用 ML5 创建了一个小型 web 应用程序，该应用程序使用该模型来生成新的诗歌，其中有一个名为“温度”的可调变量，允许你改变你在生成的文本中看到的随机性(与 AI 纯粹从文件中的诗歌数据中进行绘制相反)。

下面是这个机器人输出的一个简短的例子，供你阅读时参考:玉米上的鸟——太阳——和我一起在果园里的人——房子里的骨头——帽子日——他能行军的 Milly 曲——然后我开始了——死亡——一个恐惧他的眼睛太阳——然后太阳也开始了鸟——房间静止不动——我也看到了灵魂——太阳的沙漠脸——他站着的那个人——站在灵魂面前是一只蜜蜂——然后灵魂是美丽的

如果你对用 Spell 创建自己的文本机器人感兴趣，看看我用的这个视频教程:[https://www.youtube.com/embed/xfuVcfwtEyw](https://www.youtube.com/embed/xfuVcfwtEyw)

当然，如果你有兴趣看我的代码，我在 github 上有:

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png)[【nprimak】](https://github.com/nprimak)/[【Emily-Dickinson-LSM】](https://github.com/nprimak/emily-dickinson-lstm)

### 艾米莉·狄金森诗歌的神经网络训练

<article class="markdown-body entry-content" itemprop="text">

# 艾米莉·狄金森·伊斯特姆

艾米莉·狄金森诗歌的神经网络训练

运行 npm 安装

运行 npm start 来启动 http 服务器

导航到 http-server 消息中提供的 URL(可能是 localhost:8081)并添加/ml5/index.html

</article>

[View on GitHub](https://github.com/nprimak/emily-dickinson-lstm)

如果你喜欢这篇文章，可以考虑在 Twitter 上关注我 [@nadyaprimak](https://www.twitter.com/nprimak) 或者如果你需要更多进入科技行业的技巧，你可以现在就阅读我的书[平装本或 Kindle 版本的《踏入 T4》。](https://www.amazon.com/Foot-Door-Getting-Industry-Programmer-ebook/dp/B0813XNFNF)

制作艾米莉·狄金森诗歌机器人的帖子最先出现在 www.nadyaprimak.com/blog 的 T2 网站上。