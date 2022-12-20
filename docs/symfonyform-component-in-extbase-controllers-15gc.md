# extbase 控制器中的 symfony/form 组件

> 原文：<https://dev.to/alexanderschnitzler/symfonyform-component-in-extbase-controllers-15gc>

大家好，
在 2018 年圣诞节期间，我想知道是否可以在 extbase 控制器中使用`symfony/form`组件。对于不知道的人: **extbase** 是 **TYPO3** 的扩展框架。

#### tldr；

成功了！下面是一个演示:
[https://github . com/alexanderschnitzler/ext base-with-symfony-forms-demo](https://github.com/alexanderschnitzler/extbase-with-symfony-forms-demo)

#### 那么，我到底为什么要这么做呢？

在 TYPO3 中呈现表单有几十种解决方案。像 **powermail** 、 **formhandler** 、 **formz** 等等扩展。在此之上，有一个核心扩展叫做 **form** 。你猜怎么着，没错。但我从未瞄准一个羽翼丰满的竞争者。我所能提供的只是一个概念证明。

当我升级一个客户的网站时，我有了这个想法，所有的扩展都有兼容的版本，除了一个表单扩展。当我意识到表单再简单不过的时候，我想:用`symfony/form`构建表单太容易了，我能在 TYPO3 中实现吗？

如果你问自己这样的问题，你会怎么做？你谷歌一下。
我尝试的第一件事是`symfony/form standalone`,我立刻就想到了:
[https://github.com/lostedboy/symfony-form-standalone](https://github.com/lostedboy/symfony-form-standalone)

嗯，示例代码有点过时了，这是大部分工作要做的地方。我试图在 symfony 版中使用这段代码，但结果是 api 发生了变化。所以我不得不逆向工程，尝试一些东西，最终理解一些东西。直到我想出了一个可行的方案。

如前所述，我将概念证明放在了 github 上的一个演示库中，如果您有兴趣查看代码，可以查看一下。如果你想看实际操作，阅读`README.md`，遵循安装说明，你就可以开始了。

#### 好零件

如果你检查一下代码，你会发现不仅有一个简单的表单，还有一些在处理表单时非常方便的特性。

首先，你几乎总是想要得到认可。嗯，就在里面。然而，所示的解决方案不支持通过`doctrine/annotation`注释 buuuut 进行验证，这也是有可能实现的。

然后，你可能想要一个对付 CSRF 的可行方案。你猜怎么着？它在里面！做得很好！

还有什么？有些人可能想使用 twig 及其表单助手。你猜怎么着？是的，就在那里！好吧，这并不可怕，但仍然很好，不是吗？

#### 坏零件

哪里有光，哪里就有阴影。

不总是正确的，但在这种情况下，嗯，有一个缺点。首先，您需要用 composer 设置您的 TYPO3 安装。是的，不幸的是，仍然有可能只是抓取一个焦油球，并且依赖于其他包的扩展是没有价值的。但也许所有这些都有助于提高人们对使用 composer 设置 TYPO3 有多棒的认识。

另一个缺点是所有组件的初始化都发生在控制器本身。当然，一个合适的解决方案看起来会有所不同。但是，也许，这是未来开发人员的蓝图，他们希望将这一点带入 TYPO3 核心。

好了，坏的部分到此为止。

感谢阅读，直到下一个。