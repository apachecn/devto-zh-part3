# 将调查嵌入应用程序或网站

> 原文：<https://dev.to/mathieud/embedding-a-survey-into-app-or-website-1ca1>

在 kanochart.com，我们变得有点 meta，我们正在做一个关于我们自己服务的 Kano 调查。

我已经注意到一个特性正在变成一个“执行”特性。(“执行”的意思是:我们执行得越好，客户就越满意)。这个功能就是能够在你自己的网站或应用程序中嵌入 Kano 调查。(而不是像现在这样，让你的观众去 kanochart.com 完成调查)。

我认为实现这一点的唯一两种方法是 iframes 或使用 API。

API 有一个额外的优势，人们可以在前端做他们想做的事情，但这需要他们的编码技能，并且我编写和维护 API。

Iframes 是最简单的解决方案，但是我有点担心它们的坏名声。在过去，iframes 是一个真正的害虫和烂摊子。

我还应该担心 iframes 吗？还有其他解决方案吗？