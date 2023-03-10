# 体验节点快递

> 原文：<https://dev.to/therealdarkmage/a-taste-of-node-express-2f8f>

# 一种节点表达的味道

我正在帮助一名学生调试#node 框架中的#代码，我以前从未听说过这个框架#编程#编码#开发# webdev #开发社区

在用 Python 的 Flask 强行使用了[之后，我现在对创建 REST 设置有了一个很好的理解。](https://dev.to/therealdarkmage/platforms-python-practice-projects-and-picking-up-flask-a-blog-story--2mbl)

基本上，TL；it 博士认为，现代网站是这样做的:

```
www.somesite.com/myrest/login
www.somesite.com/myrest/logout
www.somesite.com/myrest/getuserinfo
www.somesite.com/myrest/dostuff
www.somesite.com/myrest/post
www.somesite.com/myrest/etc 
```

Enter fullscreen mode Exit fullscreen mode

注意这里非常好的结构。这是为了组织而故意做的。我完全不习惯这样的成长。没人教我休息。没有人教我如何在 webdev 中做这样的事情。我本来甚至不想做网站开发...

这段旅程始于 11 或 12 岁左右。在我背部手术后不久，我们家通过美国在线 3.0 接入了互联网。不久之后，我开始制作网站。在此之前，一位世交送了我的第一台机器:一台 MSDOS Tandy 机器。我记得当时我想出了如何用 BASIC 编写一个简单的 4 函数计算器。我的网站都是用简单的 HTML 编写的，但即便如此，我还是设法上了一个最终幻想 7 的网页。

最近，我帮助一名学生[在一个我对](https://dev.to/therealdarkmage/finding-bugs-in-languages-frameworks-you-don-t-know-node-js-52no)几乎一无所知的语言东西中发现了一些错误，但事实证明，如果你知道一些基本的东西，你可以将它们交叉应用到你不知道的领域。

一般来说，这就是人们学习新事物的方式。

**node express** 看起来有点像 Flask，但是对于 node(咄)。它是一个 REST 类型的系统，允许您以类似的方式构造端点。像 Flask 一样，您可以在本地测试它，也可以在服务器上运行它。这使得创建定制网站变得非常容易。

我从我学生的代码中看到的很多东西是这样的:

```
app.get('/home', function(req, res) {
    // code here
});

app.post('/login', function(req, res) {
    // code here    
}); 
```

Enter fullscreen mode Exit fullscreen mode

这很酷。Javascript 很简单(这也是为什么我从来没用过它的原因，lol)，但是随着我的成长，我发现你可以非常容易地用它做一些非常酷的事情。

我一直更喜欢从底层或后端的角度工作，但每个人都试图让我担任 iOS 或 webdev 前端的角色(唉！)，而且在很长一段时间里， **node.js** 对我来说只是显得太时髦(像 Ruby 一样)而不去关心。

在这个学生之后，我要和 **node.js** 坐下来谈谈。在一个充满角度、反应和空洞的世界里，我想我是节点:D

* * *

# [如果你需要一名计算机科学导师、代码审核员，或者仅仅是一个可以和程序配对的人，请联系我](https://codementor.io/mikebell66)