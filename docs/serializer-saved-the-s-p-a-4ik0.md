# 序列化程序的重要性

> 原文：<https://dev.to/sofiajonsson/serializer-saved-the-s-p-a-4ik0>

## [T1】简介](#intro)

对于我的车型。3 项目在熨斗学校，我的任务是使用 vanilla JavaScript(一个 Ruby on Rails 后端)创建一个单页面应用程序(也称为 SPA ),并加入完整的 C.R.U.D.
单页面应用程序是一个 Web 应用程序，它加载一个**单个** HTML 页面，并在用户与应用程序交互时动态更新这个特定的**页面**。
鉴于我们已经到了《权力的游戏》的最后几集，我决定开发一款《权力的游戏》笔记应用。该应用程序的前提是生成一个充满不同角色卡的页面，用户可以在其中填充关于自己喜欢的角色的评论/注释。
该应用程序包含两个预植入模型；帖子和评论。

## 型号

帖子有一个人物`name`、`image`和`comment`属性。
[![](img/690fe419022a890719ffa20c1cb1adf5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gh5ai0s6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wqpeya5faqjccq57hod9.png) 
评论模型有`text`和一个`post_id`属性。
[![](img/249753aefbabe472a667d684640188e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U4ziPKMJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1c6t7d83onm9bcvyliwv.png)

我知道，我知道..我真的搞砸了我的命名惯例。我不仅有两次注释，还使用了 Post，这是 JavaScript fetch 请求中的一个方法。

[![Alt Text](img/4ed61244e0e197b3aeeb80319af272e5.png)](https://i.giphy.com/media/ADr35Z4TvATIc/giphy.gif)

## 实现 C . R.U.D

到目前为止，我已经能够创建一个新的角色卡，用名字、图片和评论填充它。然后，卡片会附加到页面上，并显示在预先植入的卡片旁边。我已经完成了 C.R.U.D .的创建和 T2，现在需要实现最后的元素。我在角色卡上加入了按钮**U**p 更新和**D**e 销毁。接下来，我需要从 Comments 模型中添加预先植入的文本。这比我预料的要困难得多。我创建了对适当源的获取请求，但是无法将特定值发送给下一个函数，以便将它们添加到我的角色卡的注释部分。经过几个小时的挫折后，我的同学(s/o Mera)建议我尝试通过控制台中的 my Posts 模型访问种子注释*，然后在我的 PostSerializer 的末尾添加新属性，结果成功了！我现在可以访问我的帖子评论(复数)并将它们附加到卡片上。*

[![](img/a7b86813821517b11de908e4e572e325.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y2oOc5Ub--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ogcga7jvh5hnio5502v4.png) 
对我的序列化器的这个小改动将种子评论嵌套在我的帖子模型的 Comments 属性下。

[![](img/feb968178e6637f3c824af4e4be5a380.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nTXcemqy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p4bimfco2djxb2veeroa.png) 
我现在能够创建新的评论，并通过我的评论部分将它们添加到角色卡中，并滚动文本。这绝不是记笔记和跟踪它们的理想方式，但对我的第一次水疗来说，这就够了。

[![](img/ca6a0c3331d6f04685690c94c2bce344.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ygz7OGOT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/07zs9cm27l8n12xay9nw.png)

## 探究串行器

我已经让我的项目开始工作了，但是我不知道我是怎么做的。经典。我决定研究一下**serializer**，因为在这个项目之前我甚至没有听说过它们。首先:

#### 什么是 JSON 序列化？

JavaScript 对象符号是一种将对象编码成字符串的数据格式。这种数据表示可以在服务器和浏览器之间以及服务器和服务器之间进行转换。序列化是将对象转换为字符串的过程。

#### 为什么我们的 Rails 应用程序中需要它？

如果我们的应用程序通过它的 API 使用 JSON，这是处理 JSON 的一种流行方式。确保你的应用在你的`Gemfile`中有`gem 'active_model_serializers'` gem，如果没有，添加它并运行`bundle install`

#### 序列化实现

对于我们的帖子和评论数据，我们需要用我们想要序列化的属性建立序列化器，如上面的帖子序列化器所示。现在，当我寻找额外的信息时，我遇到了几个例子，它们明确地说明了序列化程序中模型之间的关系。我的项目运行得非常好..但是这是我应该做的吗？有什么不同吗？

[![](img/b17d1af4a865c455f0913f3c4295fff8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pysz--4m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h84knt5xv566bf7nt85w.png)

我的`belongs_to`和`has_many`关系只在我的模型中陈述过，所以我去找我的导师寻求进一步的澄清。在深入研究了序列化程序 gem(它是运行序列化程序的必要组件)之后，我们发现当您通过属性序列化某个东西时，它就变成了一个`Attribute`实例。当你通过`has_many`或`belongs_to`序列化一些东西时，它就变成了一个`Reflection`实例。这两者都继承自`Field`类，该类包含基本的`name`和`value`方法。然而,`Reflection`有额外的特性，比如`link`特性能够生成到特定相关对象的链接，而不仅仅是它的属性。

## 关键要点

*   我们从服务器发送到网页的一切都只是数据。
*   它需要被正确格式化，以便我们可以适当地访问它
*   您的序列化器是一个非常有用的工具，它为从 Rails API 构造 JSON 响应提供了大量的实用工具
*   快速简单的方法是简单地向序列化程序文件添加另一个属性，但是如果您想要更全面的功能，您应该实现您的关系来建立一个`Reflection`实例而不是`Attribute`实例。

如果您只需要相关对象内部的数据，就像我一样，`Attribute`实例工作得很好。

###### 资源:

[http://vaidehijoshi . github . io/blog/2015/06/23/to-serialize-or-not-to-serialize-active model-serializer/](http://vaidehijoshi.github.io/blog/2015/06/23/to-serialize-or-not-to-serialize-activemodel-serializers/)
[https://buttercms . com/blog/JSON-serialization-in-rails-a-complete-guide](https://buttercms.com/blog/json-serialization-in-rails-a-complete-guide)