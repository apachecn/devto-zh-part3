# 我被绑架了。

> 原文：<https://dev.to/missamarakay/i-ve-been-mojaved-386k>

> ![Amara Graham profile image](img/492ea3c928a2919106017f044a4e36a2.png)阿马拉·格拉汉姆[@ missamarakay](https://dev.to/missamarakay)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)所以这是我一周以来一直在调试的整洁的东西。系好这根线。2019 年 4 月 18 日下午 23:01[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1119013053390032896)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1119013053390032896)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1119013053390032896)

我度过了愚蠢的一周。事实上，这是艰难的几周，在我的待办事项清单上增加了 10 件事，但似乎一件也没做。那我是不是成了-10x dev 了？😭

> ![Amara Graham profile image](img/492ea3c928a2919106017f044a4e36a2.png)阿马拉·格拉汉姆[@ missamarakay](https://dev.to/missamarakay)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)我做的是 Unity + Watson 的东西，这里面的变量很多。Unity 更新、Watson 服务更新、Watson SDK 更新、MBP 更新、iOS 更新(如果适用)。2019 年 4 月 18 日下午 23:02[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1119013244344180737)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1119013244344180737)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1119013244344180737)

我可能不需要告诉这里的任何人，每一个新的框架、平台、库等等都会增加多少变量和不确定性。

> ![Amara Graham profile image](img/492ea3c928a2919106017f044a4e36a2.png)2019 年 4 月 18 日下午 23:02[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1119013416134426625)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1119013416134426625)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1119013416134426625)

开发人员倡导者在通过教程、操作方法、代码片段和其他文档补充资源来帮助开发人员社区方面发挥着关键作用。有些人甚至为文档做出了贡献！

因此，对我来说，醒来时看到 Twitter 上的一堆通知或收件箱里塞满了来自论坛的警告并不是闻所未闻的事情，尤其是在新的 SDK 发布之后。

但是在我写了一个快速的文本到语音转换的例子后，流量并没有减少。

> ![Amara Graham profile image](img/492ea3c928a2919106017f044a4e36a2.png)决定开始测试我的旧示例，因为旧的 SDK 版本应该工作得很好。2019 年 4 月 18 日 23 点 03 分[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1119013718346608640)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1119013718346608640)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1119013718346608640)

SDK 团队已经为没有非常积极主动地大声宣布这些突破性的变化有多么重要而道歉，但继续向我保证所有旧版本应该仍然可以工作。语音转文本不起作用。开发者不能仅仅更新 SDK 版本作为银弹。

> ![Amara Graham profile image](img/492ea3c928a2919106017f044a4e36a2.png)安国格雷厄姆[@ missamarakay](https://dev.to/missamarakay)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)什么都不管用。不是我刚才做的工作坊的例子，是我从一月份就开始做的项目。他们都运行，没有错误，但当 STT(语音到文本)应该工作，它只是坐在那里。2019 年 4 月 18 日下午 23:04[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1119013973834240001)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1119013973834240001)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1119013973834240001)

在接下来的几天里，我试图让我的 Unity 项目抛出某种智能错误，或者任何错误，但它就在那里。沃森助手和文本到语音继续工作得很好。

> ![Amara Graham profile image](img/492ea3c928a2919106017f044a4e36a2.png)
> 
> 我终于在 SDK 里得到一个例子抛出一个关于字节序的错误。2019 年 4 月 18 日 23 点 05 分[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1119014182287040513)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1119014182287040513)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1119014182287040513)

这已经是这次冒险的第 4 天了，出现关于字节顺序的错误只是告诉我捕获的音频有问题。这是错误的格式。

> ![Amara Graham profile image](img/492ea3c928a2919106017f044a4e36a2.png)安国格雷厄姆[@ missamarakay](https://dev.to/missamarakay)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)突破。算是吧。它与音频捕获有关，对我来说没有改变。WTF 是怎么回事。
> 
> 在 USB 耳机+麦克风上测试，可以用。2019 年 4 月 18 日下午 23:06[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1119014365481578496)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1119014365481578496)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1119014365481578496)

我有几个选择，包括把我的 MBP 内置麦克风换成外置麦克风，幸好我丈夫有一个 USB 耳机。我调整声音设置，确保我正在使用耳机，它不仅可以工作，而且不会出现任何错误。

> ![Amara Graham profile image](img/492ea3c928a2919106017f044a4e36a2.png)安国格雷厄姆[@ missamarakay](https://dev.to/missamarakay)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)快速搜索，找到这个:[support.apple.com/en-us/HT209175](https://t.co/kwke0Rs20D)
> 
> 为什么 UNITY 没有选中？2019 年 4 月 18 日 23 点 06 分[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1119014477108740101)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1119014477108740101)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1119014477108740101)

打扰一下。

我检查了我的设置(系统偏好设置>安全与隐私>麦克风)，果然，我想我已经解决了。

[![Microphone privacy settings with Unity unchecked](img/4bbfc63ede553d80c2ff73b2734df39f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TWsFY7EU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gvq31apad3ixyow3n4jk.png)

列表中的所有其他应用程序都被选中？Unity 怎么了？

> ![Amara Graham profile image](img/492ea3c928a2919106017f044a4e36a2.png)阿马拉·格拉汉姆[@ missamarakay](https://dev.to/missamarakay)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)勾选 Unity 框，看起来音频通过我的内置 MBP 麦克风进来就很好，就像它一直都有的一样。2019 年 4 月 18 日下午 23:07[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1119014585141530624)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1119014585141530624)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1119014585141530624)

我之前捕捉的是什么音频？随机静态？那是从哪里来的？

> ![Amara Graham profile image](img/492ea3c928a2919106017f044a4e36a2.png)安国格雷厄姆[@ missamarakay](https://dev.to/missamarakay)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)我被 MOJAVED 了。
> 
> 结束。2019 年 4 月 18 日下午 23:07[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1119014664309022722)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1119014664309022722)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1119014664309022722)

* * *

OSX 系统更新后，您的启用语音服务的应用程序是否决定停止工作？截至 4 月 11 日，我运行的是 Mojave 10.14.4，直到本周(似乎是在这次更新之后)，我的内置麦克风设置没有任何已知问题。不要在没有首先检查你的设置的情况下浪费一个星期的沮丧调试。

系统偏好设置>安全性与隐私>麦克风

请确定您用来采集音频的应用程序已被选中。

事先不客气。