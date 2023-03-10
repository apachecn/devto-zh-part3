# 葡萄是什么？

> 原文：<https://dev.to/torianne02/what-is-grape-19l>

在我之前的博客文章/教程中，我讨论了一个不使用 Rails 构建 Ruby RESTful API 的编码挑战。在这最后一篇文章中，我浏览了 Ruby gem 续集。今天，我想讨论另一个我在这次编码挑战之前从未听说过的 Ruby 宝石:Grape。

注意:在我讨论这个宝石的时候，这不会是一个教程，不像我之前在 Sequel 上的帖子。

### 先说第一件事:葡萄是什么？

Grape 是一个“用于 Ruby 的类似 REST 的 API 框架”,它被设计成在 Rack 上运行或者与其他 Ruby 框架一起运行，比如 Rails 和 Sinatra。当与其他 Ruby 框架一起使用时，Grape 提供了一个易于使用的 DSL，使得开发 RESTful API 更加容易。

### 等等！什么是 DSL？

如果你和我一样，仍然认为自己是代码新手，你可能会想什么是 DSL，它和 Ruby 有什么关系？当我第一次读到 DSL 时，我立即想到了网速，但我不知道这和什么有关系。经过大量的谷歌搜索，发现 DSL 代表特定领域语言。

现在你一定在想，这是什么胡言乱语？简单来说，DSL 是一种专注于特定问题/任务的编程语言。它们使开发人员更加容易，因为它删除了“特定任务的无关代码”，并允许开发人员“专注于手头的任务”通俗地说，这意味着 DSL 在后台做大量繁忙/重复的代码，这样开发人员就不必做了。

### Grape 如何充当 DSL？

根据 Grape 官方文档，它使得构建 RESTful API 变得更加容易，因为“它内置了对常见约定的支持，包括多种格式、子域/前缀限制、内容协商、版本控制等等。”

Grape 还与其他 Ruby gems 一起工作，帮助 RESTful API 变得更加容易。Grape 通过`rack-cors`宝石支持 CORS。您甚至可以通过安装`grape-active_model_serializers` gem 来使用主动模型序列化器。

我喜欢 Grape 的一点是，我仍然可以在它旁边使用熟悉的宝石，这使我更容易理解。有许多 gem 对它进行了补充，还有一些专门为使用 Grape 而构建和维护的 gem，就像`grape-active_model_serializers` gem 一样。

### 葡萄社区项目

在 Grape 的官方文档中，有一个社区项目页面，列出了 Grape 当前正在构建/维护的所有项目，这些项目与 gem 一起工作，有助于提供更流畅的构建 RESTful API 的体验。

这个页面可以在[这里](http://www.ruby-grape.org/projects/)找到，但是这里有一些我认为值得注意的有用的列表:

[grape-entity](https://github.com/ruby-grape/grape-entity)
[grape-active record](https://github.com/jhollinger/grape-activerecord)
[grape-active _ model _ serializer](https://github.com/ruby-grape/grape-active_model_serializers)
[garner](https://github.com/artsy/garner)
[grape-CORS](https://github.com/adamluzsi/grape-cors)
[grape-batch](https://github.com/c4mprod/grape-batch)
[grape-route-helpers](https://github.com/reprah/grape-route-helpers)
[grape _ logging](https://github.com/aserafin/grape_logging)
[grape-swagger](https://github.com/ruby-grape/grape-swagger)
[grape-swagger-entity](https://github.com/ruby-grape/grape-swagger-entity)

### 最后的想法

我真的很喜欢用葡萄。它的简单性使得它非常容易理解和使用。我相信 Grape 是一个非常有用的工具，可以帮助在 Ruby 中构建干净高效的 RESTful API。我真的很喜欢我的代码看起来是如此的干净，以及它是如此的一目了然。我是一个经常评论我的代码的人，所以我觉得没有必要这样做本身就是一个胜利。

我也很喜欢其他与 Grape 相关的 gem，它们增加了功能，并由 Grape 团队构建和维护。它们还允许人们做出贡献，这对于任何正在寻找开源项目的人来说都是一个好主意。

我希望这个关于 Grape 的快速概述能激发你在未来的项目中使用它。我迫不及待地想获得更多的经验。

编码快乐！

#### 来源

[红宝石葡萄](https://github.com/ruby-grape/grape#active-model-serializers)
[红宝石葡萄:历史与团队](http://www.ruby-grape.org/team/)
[用葡萄制作 Rails API](https://www.thegreatcodeadventure.com/making-a-rails-api-with-grap/)