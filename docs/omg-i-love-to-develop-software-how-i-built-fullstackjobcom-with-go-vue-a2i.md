# OMG，我喜欢开发软件(我是如何用 Go、Vue 建立 fullstackjob.com 的……)

> 原文：<https://dev.to/golangch/omg-i-love-to-develop-software-how-i-built-fullstackjobcom-with-go-vue-a2i>

首先简单介绍一下我的背景...大约 14 岁的时候，我开始接触魔鬼。是的，今天这只是太晚了，但 35 年前这只是好的:-)这是我叔叔的辛克莱 ZX 81。首先是计算机 ZX 频谱，然后它确实通过雅达利街/TT 到个人电脑的。
我没有学过计算机科学，完全是另一回事，但大约 24 岁时，我有机会进入了一家非常早期的本地互联网提供商(拥有 6 个模拟 Zyxel 调制解调器和 128 KB 主干网…)作为“站长”。
回想当年，我就像今天一样，被称为“全栈开发人员”或者更多。我做门户网站之类的东西，用 M$技术，所以 MSSQL，ASP/ASP。NET，意思是前端/后端、DBA 和服务器上所有与 IIS 相关的东西(类似于 devops :-)，此外还向客户解释了如何连接 Internet Explorer 1。omg…再拉几十年吧…

做了大约 10 年的程序员后，我成立了自己的公司，在架构和管理任务和职位方面做得越来越多。我不得不停下来在工作的时候黑东西，但总是继续学习，在周末黑一些私人的无聊的东西。a)因为我喜欢它，b)程序员不能告诉我每个故事；-)

在过去的 3 年里，我担任软件生产主管，我们在那里为电信/消息行业开发非常复杂的软件。但是就像我之前写的，我是“管理者”，编码和架构别人做。

所以最后我决定做一些事情，不仅对我的垃圾桶毫无意义，而且对其他人有用，甚至可以利用时间做生意。我想到了一些想法，这些想法并不新颖，也不会改变世界。这是一个工作板引擎，允许专业社区和公司运行自己的工作板。这需要更长的时间，直到有东西要展示(必须是多租户/语言/文化/tz 等，可扩展等)…所以我首先从结果开始，这是一个引擎将“呈现”，这是一个工作板，在我的背景下思考，仅供全栈开发人员使用。

首先，我选择了“全栈式”:

[![](img/e9cf95c8e850cc6ae8f0b1c186270968.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8ays0XUM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1h5gefkfvusn1s2qayux.png)

# 后端:

golang 我从 3.5 年开始接触，我们在公司密集使用它，所以我用 Go。我把 Python 放在列表中，因为我想把它作为一个副作用。ArangoDB，Redis 和 Nsq 我过去也做过一些无聊的事情。

```
API: Golang, Chi
Web Server: Golang
DB: ArangoDB (for now just documents, but have some cases in mind for graphs)
Redis for Sessions
Nsq for Queue
Some Scripts in Python 
```

Enter fullscreen mode Exit fullscreen mode

# 前端:

我去年用 React/Redux 和 Angular 234567 做了一些私人的东西…很可能我会用 Angular 做引擎的管理部分。我真的很喜欢 ES6 上的 Typescript，对于复杂的项目，Angular 可能是一个很好的选择。

但对于板前端，我必须在已知的反应或未知的反应之间做出选择。我选择了另一个副作用 goody 和 Vue.Js。直到现在，我只是觉得有趣。我真的很快得到了结果，主要是因为非常好的文档

```
End User Portal: Vue.JS, ES6, Bootstrap 4
Admin Section: Probably Angular 8 
```

Enter fullscreen mode Exit fullscreen mode

有什么可看的吗？哦是的！

所以我从过去的 5 个周末开始，做了以下事情:

```
Portal with Listing of Full-stack Developer Jobs, with client-side filter/sorting/paging
Job Submit Form with Email Notification to admin
Subscription for Job Newsletter
Simple Blog “Engine”
Some SEO related work
Social Sharing for Jobs and Blog Posts 
```

Enter fullscreen mode Exit fullscreen mode

所有这些都托管在裸机服务器上，使用 Docker/Rancher 进行部署，并作为可伸缩性途径的一部分。

不可见的是，我已经在 API 端实现了拥有特权和角色的身份验证/授权服务，注册/登录/管理部分的我端点。

# 请看一下:-)

全栈开发者职位:[https://fullstackjob.com](https://fullstackjob.com)

[![](img/11d2052eb8b7bb74c07209690c249d6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mJd3CLBY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tdekgu70r9mt5bhtwjjr.png)

是的，“OMG，我喜欢开发软件”这个标题是如此的真实。我决定回到我的根本，离开这种管理的东西，回到我过去的日子里找点乐子。如果你想从这篇文章中学到什么。我认为是这样的:
好的开发人员、程序员、软件架构师不是一次性努力达到某个程度，而是一直学习和实践。这很有趣，如果你喜欢黑客的话:-)

顺便说一句…除了 https://fullstackjob.com/blog，我经营#Golang 博客已经两年了: [https://golang.ch](https://golang.ch) (推特[https://twitter.com/golangch](https://twitter.com/golangch)

感谢您的阅读，并为我的“边做边学”英语感到抱歉(是的，即使我没有学习，但结果不如软件开发好)

(更多内容/联系我:[https://www.linkedin.com/in/stefanwuthrich/](https://www.linkedin.com/in/stefanwuthrich/))
原帖:[https://medium . com/@ golangch/OMG-I-love-to-hack-how-I-build-fullstackjob-com-with-go-vue-f 561680 c 6 CEB](https://medium.com/@golangch/omg-i-love-to-hack-how-i-built-fullstackjob-com-with-go-vue-f561680c6ceb)