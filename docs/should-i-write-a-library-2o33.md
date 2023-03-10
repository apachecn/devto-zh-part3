# 该不该写库？

> 原文：<https://dev.to/skuzzle/should-i-write-a-library-2o33>

每个中型项目可能都经历过这样一个时间点，其成员需要决定是否将公共代码外包给自己的库工件。这样做的主要原因是为了节省重复的代码和*干*(不要重复自己)。可悲的是，我已经看到这在一些项目中事与愿违，重复代码可能是更好的解决方案。这是因为:

> DRY 有一个主要的缺点:它引入了依赖性。

假设您在服务中有相同的代码部分 **A** 和 **B** 。这些部分是完全独立的。如果服务 **A** 的实施要求发生变化，服务 **B** 可以保持不变。这是一个非常舒服的情况，因为它给了你很大的自由来应用改变。

如果您将公共代码移动到它自己的库工件中，并且服务需求 **A** 正在改变

*   您更新了库代码，并将其作为新版本发布
*   您更新了服务 **A** 中的库依赖项

根据我的经验，接下来会发生以下两种情况之一:

1.  您忘记更新服务 **B** 中的库依赖关系
2.  您更新了服务 **B** 中的库依赖项，发现您的更改破坏了 **B** 中的实现
3.  您更新服务 **B** 中的库依赖项，发现您的最新更改在 **B** 中工作正常。不幸的是，您破坏了一个不相关的部分，因为您的库的最新版本还包含您忘记更新依赖项的早期更改
4.  您在 **B** 中更新了库依赖，一切都如预期的那样工作(是的，当然)

随着使用你的库的每一个进一步的服务，这些问题显然变得更糟。您现在需要更新库的每个客户端，尽管您需要的更改只影响一个客户端。或者您选择不主动更新其他服务中的依赖项，稍后执行大型迁移(或者从不执行)。

在漫不经心地提取常见代码之前，请对以下主题进行一些思考。

## 选择范围

在决定将公共代码移动到库中之前，您必须理解*为什么*您在不同的位置有相同的代码。这可能是纯粹的巧合，因为在服务 **A** 和 **B** 中有相似的(但实际上*不相关的*业务需求。业务需求可能会以不可预见和不兼容的方式发生变化。因此，试图将您的业务逻辑统一到一个库中可能不是最好的主意。

另一方面，如果您的代码纯粹用于技术目的，比如管理数据库连接、日志、服务 REST 端点等等，那么考虑将这些东西重构为可重用的库是非常值得的。如果这样一个技术方面发生了变化，那么这些变化很可能对代码的其他用户也是有益的。

当决定对常用代码使用共享工件时，不要犯将不同的关注点混合到这个工件中的错误。不要把数据库连接管理和日志代码放在一起。应该只有一个原因可以解释你为什么要对你的库进行修改。这也减轻了第 3 点。因为我们正在减少打破不相关的部分的可能性。

不要以编写一个以后可能被多个客户使用的库来开始你的项目。你还不知道实际的需求，并且强调了错误的部分。简单来说:

> ![TJ Stankus profile image](img/29dee4db8a9cf6e5e1f90d5bdbd9043b.png)TJ stan kus[@ TJ stan kus](https://dev.to/tjstankus)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)今天和一个同事讨论 DRY，我终于找到了我想说的话:一旦存在重复，考虑消除它是好的，但防止它发生通常会变得不必要的复杂。从类似的代码中可以收集到很多信息。00:13am-2018 年 4 月 24 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=988571579536826368)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=988571579536826368)342[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=988571579536826368)907

## 管理变革

如果您将代码库的一部分提取到一个库中，您必须为即将到来的变化以及您希望如何处理它们做好计划。

*   将库代码设计成可从“外部”扩展
*   制定一个迁移策略，以防需要更改库
*   从长远来看，有一个能够从库中删除代码的弃用策略
*   有一个通知策略来宣布新的版本

为可扩展性而设计可能会让人精疲力尽。您必须考虑有效的扩展点，并需要预测未来的需求。可能总是有一些显而易见的接口可以很容易地定制，但是有*会有*是你直到实际需求出现时才知道你需要的接口。很有可能你会投入大量的时间提前计划并过度设计你的代码，以便为即将到来的每一个可能的变化做好准备。或者，您不这样做，并最终每隔一天应用更改并发布新的库版本。

你已经放弃了对你的代码库的每一部分进行修改的自由。现在，无论何时你必须改变库中最小的部分，你必须记住你可能会破坏依赖于这个部分的代码。根据 [Hyrums 定律](http://www.hyrumslaw.com/)，如果你的库有足够多的用户，你不仅*可能*破解，而且你实际上*会*破解代码:

> 有了足够数量的 API 用户，
> 你在合同中承诺什么并不重要:
> 你的系统的所有可观察的行为
> 将依赖于某人。

如果没有弃用策略，你最终会带着旧代码走很久。如果您没有办法通知客户不再使用的类和方法，您将要么在这个库的生命周期内维护这个代码，要么在没有通知的情况下删除它，在客户迁移到下一个版本时让他们措手不及。

现在开始感觉更像是构建一个*产品*，而不是做简单的代码移动重构。你的图书馆拥有的用户越多，这些单一主题的权重就越大。另一方面，你的图书馆的用户越少，节省努力的效果就越小。在维护重复的代码和维护对库的更改之间，这是一个不断的权衡。

# 总结

管理一个库的开销可能是令人生畏的，尤其是当你需要与一个实际的项目并行的时候。另一方面，如果你的总体目标是编写、发布和维护一个库，这些主题会给你一个很好的开始。

我试图强调在项目中引入共享代码时必须考虑的权衡。

那么，应该写一个库吗？

在软件开发中，通常没有二进制的*是*或*否*(这有点讽刺，不是吗？)但只有好的 ol' *看情况*。