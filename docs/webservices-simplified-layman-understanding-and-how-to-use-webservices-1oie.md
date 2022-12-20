# 简化的网络服务——外行理解，以及如何使用网络服务

> 原文：<https://dev.to/innoraft12/webservices-simplified-layman-understanding-and-how-to-use-webservices-1oie>

我一直都知道 web 服务以及与 web 服务生态系统相关的几十万个首字母缩写词，但我从未真正编写过使用它们的代码。我避免和他们一起工作，因为，我不知道为什么，我总是认为他们是任何抗议者对政府的看法。好吧，我只是说我不知道为什么我会那样想，所以你可以自由地假设你的理由，我担心大多数理由都是对的。但这只是时间问题，在此之前，我无法避免与网络服务打交道，并开始真正了解它们。他们远不像我们星球上的政府那样无聊和笨拙。相反，它们是一种简单而强大甚至相当有趣的东西。现在我不再讨厌和回避网络服务，我必须分享我所知道的关于它们的好的方面。

我喜欢简化所有事情，尤其是与技术相关的事情，除非我有心情搞些极客主义:-)所以如果你在这里找到的答案和信息听起来与你在大多数其他地方找到的不同，不要感到惊讶。

什么是网络服务？web 服务是任何一种软件，它提供一些功能，可以在互联网上获得，并使用标准化的 XML 进行交流。

上面答案中的“标准化”部分让 web 服务看起来很无聊，但只有当你对事物有错误的看法时才会如此。这里的标准化有巨大的好处，这也是 web 服务成为一个好主意的原因。让我告诉你怎么-

让'你' =你或任何应用程序或只是一段代码或其他东西。现在..你可以通过 HTTP、SMTP、FTP、BEEP 和许多其他协议在网络上与 web 服务对话。
当您与 web 服务对话时,“对话”部分是以标准化的方式使用 XML 完成的。当前的标准化方式是 XML-RPC 和 SOAP。
一个 web 服务为你做的所有事情(提供的功能)和它需要你做的所有事情(期望的输入)都以一种叫做 WSDL 的标准化方式用 XML 描述。
web 服务存在于注册表中，因此很容易找到合适的工作。这是通过另一种叫做 UDDI 的标准化 XML 方式实现的。
XML 是让 web 服务起舞的音乐
以上所有这些都是与 web 服务相关的真正的基本思想，但是当然我会鼓励你学习更多的东西和细节

我能在 webservices 上找到的最好的常见问题解答-[http://www.webservicex.net/WS/faq.aspx](http://www.webservicex.net/WS/faq.aspx)
一篇文章的祝福，揭开了与 webservices 相关的首字母缩写词和更多-[http://www.xml.com/lpt/a/888](http://www.xml.com/lpt/a/888)
开始使用 web services-
说我们需要获取一家上市公司的股票报价，并在我们的应用程序中使用一些数据。我们可以在公共网络服务库上寻找允许我们这样做的网络服务，比如 http://www.service-repository.com/和 www.webservicex.net/WS/wscatlist.aspx。或者你可以请求使用付费网络服务，例如路透社有一个非常全面的付费网络服务，提供所有能想到的与股票和金融市场相关的功能。

让我们在这里使用“股票报价”web 服务-[http://www.webservicex.net/WS/WSDetails.aspx?WSID=9&CATID = 2](http://www.webservicex.net/WS/WSDetails.aspx?WSID=9&CATID=2)，它出现在后来的公共存储库中。让我们使用流行的 perl 的 HTTP::Request 模块来完成这项任务(这一点以及更多使用 web 服务的方法将在本文后面讨论)

更多信息请访问:[简化的 web 服务-外行理解，以及如何使用 web 服务](https://www.innoraft.com/blogs/webservices-simplified-layman-understanding-and-how-use-webservices)