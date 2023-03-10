# 理解设计模式:单例使用英雄的例子！(蝙蝠侠和蜘蛛侠在里面)。

> 原文：<https://dev.to/carlillo/understanding-design-patterns-singleton-using-hero-examples-batman-and-spiderman-are-inside-2pm5>

有 23 种经典的设计模式，在原书《设计模式:可重用面向对象软件的元素》中有描述。这些模式为软件开发中经常重复出现的特定问题提供解决方案。

在这篇文章中，我将描述如何使用**单例模式；以及**如何以及何时应用。

## 单例模式:基本思想

> 在软件工程中，* *单例模式 *是一种软件设计模式，它将一个类的实例化限制为一个“单个”实例。当只需要一个对象来协调整个系统的动作时，这很有用。这个术语来自独生子女的数学概念。* —维基百科
> 
> 确保一个类只有一个实例，并提供一个全局的访问点。—设计模式:可重用面向对象软件的元素

这种模式的主要特点是每个类中只有一个对象被实例化。此外，还为类创建了一个入口点，通常使用一个访问器方法，比如`getInstance`。

这个模式的 UML 图如下所示:

[![](img/6e51437cf5084671dfd7fa7fedba96ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SNlp4BDq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2224/0%2ASh3qaWe7E5IHnKAS.png)

Singleton 类是一个单独的类，它有一个名为`uniqueInstance`的属性，用来存储 Singleton 类的一个实例。类构造函数是私有的，您只能通过一个访问器方法来访问实例，这个方法可能是`getInstance`。

访问器方法负责在单个实例存在的情况下返回该实例，或者在尚未实例化的情况下实例化该实例。

在下列情况下，应该使用单例模式:

1.  一个类必须有一个实例，并且这个类必须可以被客户端从它们知道的访问点访问。

2.  singleton 类可以通过继承来扩展，客户端必须能够使用扩展的类，而无需对其进行任何更改。

单例模式有几个优点，总结如下:

*   严格控制客户端如何以及何时访问单例实例。所以，你有一个受控的访问，因为单例类封装了它的实例。

*   当您需要限制我们从一个类创建的实例的数量以节省系统资源时。

*   单例模式是对全局变量的改进，因为它避免了用只存储单例实例的全局变量来污染名称空间。

*   代码更容易使用、理解和测试，因为单例简化了代码。

我现在将向您展示如何使用 JavaScript/TypeScript 实现这种模式。在我们的例子中，我虚构了一个问题，其中有一个名为`DatabaseConnection`的类，它定义了两个属性:configuration 和`getUniqueIdentificator`。这个类是到我们数据库的连接。`DatabaseConnection`由多个客户端使用(`client1`和`client2`)。下面的 UML 图显示了我刚刚描述的场景。

[![](img/23e1e37e9dd699bbc41f725c01bdde40.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tggxBLqj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AQWJPNUhtZFCUExEa.png)

客户代码关联如下:

[![](img/8981a82a933082036ca4d02ab155c4aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FGJg-GHo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2704/1%2A12qfk73e5NNCztd9lXmAQw.png)

[![](img/5cb062a45d02da66f3755dd864266d4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bsc31twl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2704/1%2A1nTWDZjtPtYcLvO-bweLPw.png)

每个客户端创建一个到数据库的新连接，并请求每个连接的唯一标识符。这种体系结构的一个主要后果是使用了过多的资源。

`DatabaseConnection`类如下:

[![](img/b27bcff8c97659e461dd67cdd860d1ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mvxvrSVE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2776/1%2AtZO2sPmuzfQNQIG-LgJwCA.png)

在前面的类中可以看到，只有私有属性可用于数据库的配置，唯一标识符是使用公共属性访问的。

最后，该交互的示例代码如下:

[![](img/3a83bac223d6e681371ebb671f7e6f9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xTQFm8vv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2200/1%2A-vrzi_OnICauW9pOhOIqUA.png)

获得的结果如下图所示:

[![](img/ddd2976a4f54cefeffc752c6f67de45e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WKF3khyc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2ANhpzvr12YQB1Szpr.png)

如您所见，数据库的每个实例都有唯一的标识符，因为它们是不同的实例，而它们执行的任务完全相同。事实上，最聪明的做法是用一个实例来建立连接。

解决方案是使用单例模式，该模式只创建该类的一个实例。也就是说，使用单例模式的新 UML 图如下所示:

[![](img/d17ec4dcf96b2a894decb8cd45388a92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TWBELWXE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AvtQnCEiEqSayfE7I.jpg)

与`DatabaseConnection`关联的代码如下:

[![](img/7a92f6108871b8e61de3c8e2c6e19943.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lWmLO9Tp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2776/1%2APzh54Jo5GmOaaJGeDTe1XQ.png)

对该实例的唯一访问点是使用 getDatabaseConnection 静态方法，该方法将创建一个新实例，以防该实例不存在或将获得它。通过这种方式，客户端稍加修改即可使用该实例，而不是创建自己的实例:

[![](img/c752daad078edce831b9f5ee7871f206.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OG25wVJu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3176/1%2AO5zA3cxV4gn9OLhQdeYArw.png)

在程序执行过程中进行这些修改后的结果如下图所示:

[![](img/c0ed9bcec15c9faf740dfc099b313299.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KQrQnDkj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2Au7P-fBMkBJeIB2jO.png)

我已经创建了两个 npm 脚本，在应用了 Singleton 模式后，它们运行这里显示的两个例子。

`npm run example1-problem`
T1】

另一个使用单例模式解决的有趣例子是当有几个类必须是单例的时候。例如，蜘蛛侠和蝙蝠侠等一系列英雄都是单身。在下面的 UML 图中，您可以看到这种情况:

[![](img/b25cf36c69a540ca2938e10e41395c5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e6TxBsqh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AR8FDjwDvG6zIGhI5YbVYKQ.jpeg)

与客户端关联的代码如下:

[![](img/596616286e97d843e394674a6aa3adb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z7PPDTpn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2096/1%2AwqEj8ZGPFr4StMR_E_eURg.png)

[![](img/dc1f7e64b7adc83c1fc8b79e3cb4c66a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DIisEhZO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2096/1%2AU-mfIY0homzPj4bAljTrCQ.png)

[![](img/df8eecafa8119625e19b561cb8b0173d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BKX-mJEu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2800/0%2AWVVpLdidFpeY4-JS.jpg)

接下来，我们将创造我们的英雄，这将是独一无二的。首先，我们将定义一个信息的公共接口，每个接口将包含:

[![](img/7ab3814ef0ba7feb154e4f9e9e908701.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--76fehidF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AZ16IFRhhTIsb_rYip9PV0g.png)

我们的英雄是独一无二的，但有着共同的属性和方法，为此我们定义了一个名为`HeroBase`的父类，它包含了蜘蛛侠和蝙蝠侠的共同特征。这个类是下面的一个:

[![](img/c25744ba6dd1a22c0e358d18ae1baf89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9Iu-ivtB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3144/1%2AYPkw72omidaOgq-nCBL3Yg.png)

蝙蝠侠和蜘蛛侠都在他们的构造中实现了单例模式，并存储了对每个类的唯一对象的引用(我们的英雄！).这些类别如下:

[![](img/208f1f8aedd9fb3075a85ba85f9e2c3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gDAxXrrr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2232/1%2Ab1x7t4NpUdv9yG4_eEFGiw.png)

[![](img/f20087763cb13459a993891314146eeb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wm6BuP9---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2232/1%2AXg9rf7mL6J_6MJ8Dk7oUbQ.png)

最后，该交互的示例代码如下:

[![](img/b700bbdf2a8506ad2901c2e6607ade3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0F56dd6E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4088/1%2AjFdAqxbwD8kHQvclNNvqvw.png)

获得的结果如下图所示:

[![](img/477eef2b0c3708fe3956c618fbedc9fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NBe2XVbi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2132/0%2A0gAIvBctSKBOzNTh.png)

我已经创建了一个 npm 脚本，它在应用了 Singleton 模式后运行这里显示的例子。

`npm run example2-singleton-solution1`

单例模式可以避免项目的复杂性，因为您可以在客户熟知的单点上严格控制类的实例化。此外，这是一种节省系统资源的模式，因为不用实例化一组执行相同任务的类，而是使用该类的单个实例。然而，这种模式名声很差，甚至被认为是反模式，因为这种模式实际上创建了全局变量，可以从代码中的任何地方访问和更改这些变量。

最重要的不是实现我向你展示的模式，而是能够识别这个特定模式可以解决的问题，以及你何时可以或不可以实现所述模式。这一点至关重要，因为实现会因您使用的编程语言而异。

## 结论

单例模式可以避免项目的复杂性，因为您可以在客户熟知的单点上严格控制类的实例化。此外，这是一种节省系统资源的模式，因为不用实例化一组执行相同任务的类，而是使用该类的单个实例。然而，这种模式名声很差，甚至被认为是反模式，因为这种模式实际上创建了全局变量，可以从代码中的任何地方访问和更改这些变量。

最重要的不是实现我向你展示的模式，而是能够识别这个特定模式可以解决的问题，以及你何时可以或不可以实现所述模式。这一点至关重要，因为实现会因您使用的编程语言而异。

## 越多越多...

*   设计模式:可重用的面向对象软件的元素，伽马，赫尔姆，约翰逊，& Vlissides，艾迪森韦斯利，1995 。

*   [单例模式——维基百科](https://en.wikipedia.org/wiki/Singleton_pattern)。

*   [https://www . do factory . com/JavaScript/singleton-design-pattern](https://www.dofactory.com/javascript/singleton-design-pattern)

*   [https://github . com/sohamkamani/JavaScript-design-patterns-for-humans #-singleton](https://github.com/sohamkamani/javascript-design-patterns-for-humans#-singleton)

*   这个帖子的 **GitHub** 分支是[https://GitHub . com/Caballerog/blog/tree/master/singleton-pattern](https://github.com/Caballerog/blog/tree/master/singleton-pattern)

*最初发布于[https://www . carloscaballero . io](https://www.carloscaballero.io/design-patterns-singleton)2019 年 5 月 2 日。*