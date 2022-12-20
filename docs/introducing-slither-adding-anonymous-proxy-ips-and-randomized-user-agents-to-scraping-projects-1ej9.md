# Slither 简介:向抓取项目添加匿名代理 IP 和随机用户代理

> 原文：<https://dev.to/kaelscion/introducing-slither-adding-anonymous-proxy-ips-and-randomized-user-agents-to-scraping-projects-1ej9>

嘿德弗思！好久不见！在过去的一段时间里，我一直在度假，我想我会带着我的第一个开源项目 Slither 回来。Slither 是一个基本的匿名化框架，用于将 elite、https、匿名代理 IP 和伪随机用户代理添加到您的 web 抓取和/或笔测试项目中！Slither 是我第一次涉足 OSS，也是我非常关心并希望人们会发现有用的东西。

GitHub 回购可以在[这里](https://github.com/kaelscion/slither)找到，我真的希望你们都喜欢它！网上有很多关于这个话题的请求和问题，以及当你试图收集和/或聚合数据时，如何更容易地避免可怕的 403(或者更糟的 503 )!该框架非常简单，支持并发抓取和并行处理。

每当 Slither 类的一个实例被声明时，一个 IP 和用户代理的列表就会从网络上的代理站点中被取出，并被分配给`Slither().ip`和`Slither().ua`变量。只需将这两个变量插入到项目的头文件中，然后就可以开始运行了！

我真的希望这能帮助一些网络抓取的新手和收集数据的新兴领域的数据科学家和 ML 工程师使用！请尝试一下，并在这里或回购上留下评论。尽管我从事软件工作多年，但这是我的第一个 OSS 项目，所以请温柔点😝。享受和快乐刮刮！