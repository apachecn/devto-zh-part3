# 强化应用的七种方法

> 原文：<https://dev.to/heroku/seven-ways-to-fortify-your-application-4p4j>

*这篇博客文章改编自 Amy Unger 在 RailsConf 2018 上的一次演讲，题为“[旋钮、按钮&开关:大规模操作您的应用](https://www.youtube.com/watch?v=KjfYoHxmCmI)*

我们都见过当单个上游服务关闭时应用程序崩溃的情况。尽管我们的初衷是好的，但有时意外的停电会让我们不得不匆忙进行维修。在这篇博客文章中，我们将看看一些你可以在灾难来临之前集成到你的应用程序中的工具。我们将讨论七种策略，它们可以帮助您减少负载、优雅地失败并保护苦苦挣扎的服务。我们还将讨论这些技术的技术实现——特别是在 Ruby 中，尽管这些课程适用于任何语言！

[https://www.youtube.com/embed/KjfYoHxmCmI](https://www.youtube.com/embed/KjfYoHxmCmI)

## 1。维护模式

对于许多应用程序来说，将应用程序从一个活跃的站点转换成一个单一的、停机的页面是一个巨大的挑战。然而，当您不确定实际问题是什么时，它也可能是唯一的选择，并且它也可能是小型应用程序或微服务的完美工具。因此，它应该是要建立的第一个安全措施之一。

当你进入维护模式时，你的网站应该有一个清晰的信息，并带有一个状态页面的链接，这样你的用户就知道会发生什么。更重要的是，维护模式应该非常容易切换。在 Heroku，我们将它实现为一个环境变量:

```
MAINTENANCE_MODE=on 
```

虽然有许多方法可以实现这种模式，但是您的应用程序操作人员应该能够轻松地切换是下午 2 点还是凌晨 2 点！

## 2。只读模式

对于修改任何数据的应用程序，只读模式有助于为您的用户保留最少的功能，并让他们相信您没有丢失他们的所有信息。这看起来像什么取决于你的应用程序提供给用户什么功能。也许它将数据存储在数据库中，将文件上传到文档存储中，或者更改保存在缓存中的用户偏好。一旦您知道您的应用程序正在修改什么，您就可以计划如果用户*不能*修改它会发生什么。

假设一个不良因素导致复制延迟急剧增加，所有用户都无法再对站点进行任何更改。与其关闭整个应用程序，不如进入只读模式。这也可以设置为一个简单的环境变量:

```
READONLY_MODE=on 
```

与全面的维护模式相比，客户通常更喜欢只读模式，这样他们就可以检索数据并知道最近进行了哪些更改。如果您知道您的站点仍然能够为其访问者提供服务，那么您的只读模式应该通过一个横幅(或一些其他 UI 元素)来表明，在正在解决的问题期间，某些功能将被暂时禁用。

## 3。功能标志

通常，引入功能标志是作为 A/B 测试新站点功能的一种方式，但它们也可用于在事件发生时进行处理。

有三种不同类型的功能标志需要考虑:

*   用户级:这些标志在每个用户的基础上切换。在停机期间，由于它们的狭窄效应，它们可能不是很有用。
*   应用程序级:这些标志影响站点的所有用户。这些可能更像上面列出的维护模式和只读模式切换。
*   组级:这些标志只影响您之前确定的用户子集。

就事件处理而言，组级功能标志是三者中最有用的。您需要考虑哪些分组对您的应用程序有意义；这些最终是你想要控制什么和你的应用程序的用户是谁的组合。

假设您的应用程序已经开始向有限数量的用户销售产品。一天晚上，出现了一个关键问题，需要禁用该功能。我们在 Heroku 的代码中实现了这一点。单个类可以回答关于当前应用程序状态和切换功能的问题:

```
ApplicationSetting.get('billing-enabled')
=> true 
```

这个`ApplicationSetting`模型可以由一个数据库、Redis——任何提供最大弹性的东西来支持，以确保这个检查不会失败。

根据您公司对稳定性的需求，进一步细分为更小的细分市场可能是有意义的。例如，您的欧盟用户可能有一个完全不同的计费功能标志:

```
ApplicationSetting.get('billing-enabled-eu')
=> false 
```

对于早期阶段的公司来说，有这么多层次的细化可能是愚蠢的，但是如果您的指令是将客户影响降低十分之几的百分比，那么您将感谢对应用程序的哪个部分受到影响的信心！

## 4。费率限制

速率限制旨在保护您免受不尊重和恶意的流量，但它们也可以帮助您减轻负担。当您接收到正常和恶意流量的混合时，您可能需要人为地降低速度，同时找到问题的根源。

如果你需要减少一半流量，那就减少一半流量。您的合法用户可能需要尝试两到三次才能处理一个特定的请求，但是如果您让他们明白您的服务是出乎意料的(但是故意的！)拒绝相当数量的请求，因为这是在某种负荷下，他们会理解并调整自己的期望。

速率限制还可以保护依赖您的服务的其他业务部门对您的应用程序的访问。通常，用户看到的单个应用程序实际上是不同服务的网格，这些服务共同作用来创建单个用户体验。虽然在其他服务关闭时，您完全可以让内部系统正常工作，但是优先处理内部请求比外部请求更容易。

在 Heroku，我们将利率限制作为两种不同杠杆的组合来实施:对每个账户使用一个单一的默认值，再加上针对不同用户的额外修正值。我们发现这使我们能够灵活地为某些用户提供他们需要的速率限制，同时保持对我们在任一点能够处理多少流量的单一控制。

我们将该值设置为具有全局速率限制默认值的应用程序设置:

```
ApplicationSetting.set('default-rate') = 100 
```

这里，我们假设每分钟 100 个请求——希望您的站点可以处理更多的请求！接下来，我们为所有用户分配一个默认修改器:

```
user.rate_limit_modifier
=> 1.0 
```

每个用户都从一个修饰符开始。为了确定客户的速率限制，我们将应用程序默认值乘以他们的修改量，以确定他们的速率限制应该是多少:

```
user.rate_limit
=> 100.0 # requests per minute 
```

假设一个超级用户写信来支持并提供需要两倍速率限制的合理理由。在这种情况下，我们可以将它们的修改器设置为两个:

```
power_user.rate_limit_modifier
=> 2.0 
```

这将授予他们每分钟 200 个请求的速率限制。

在某些时候，我们可能需要减少我们的流量。在这种情况下，我们可以将速率限制减半:

```
ApplicationSetting.set('default-rate') = 50 
```

现在每个用户的默认速率限制减半，包括上面的超级用户。但是他们的值 100 仍然比其他人的默认值 50 高一点，这样他们就可以继续他们重要的工作。

像这样设置限制使我们能够快速调整进入的流量，而不必对每个用户运行脚本来调整他们的速率限制。需要注意的是，根据您的应用，您可能需要考虑基于成本的速率限制。使用基于成本的速率限制系统，您根据用户请求的长度向用户“收取”一定数量的令牌，这样他们就不能像您的超快端点那样频繁地调用您的真正慢的端点。

最后，这可能看起来违背直觉，但是速率限制的算法越复杂，在拒绝服务攻击中就越糟糕。你说你不能处理一个请求所花费的计算时间越多，当你处理大量的请求时，情况就越糟糕。如果需要，这不是不实现复杂的速率限制的理由，但这是确保您有其他层来处理分布式拒绝服务攻击的理由。

## 5。停止非关键工作

如果您的应用程序不断突破其基础设施的极限，您应该能够拔掉任何不紧急的东西。例如，如果有任何不需要立即完成的工作或流程，您应该能够关闭它们。

让我们看看如何在生成每月用户报告的函数的上下文中实现这一点:

```
class MonthlyUserReport
 def run
   do_something
 end
end 
```

很有可能在计算上非常昂贵。我们可以修改这个类，首先断言可以生成报告*和*:

```
class MonthlyUserReport
 def run
   return unless enabled?
   do_something
 end

 def enabled?
   ReportSetting.get("monthly_user_report")
 end
end 
```

现在，在我们做任何工作之前，我们可以检查以确保生成已启用。就像上面的应用程序设置一样，我们在这里将`ReportSetting`定义为一个模型:

```
ReportSetting.get("monthly_user_report")
=> false 
```

我们也可以推广这个实现。让我们让每月用户报告从父`Report`系统继承:

```
class MonthlyUserReport < Report
 def build
   do_something
 end
end 
```

现在，每月用户报告只负责执行构建，父类负责计算作业是否应该运行:

```
class Report
 def run
   return unless enabled?
   build
 end

 def enabled?
   ReportSetting.get(self.class.underscore)
 end
end 
```

## 6。已知的未知

有时，观察新变化的影响会超出特性标志的范围。即使你相信你所有的测试都是完美的，你仍然会带着怀疑，知道一个灾难性的结果正在阴影中逼近。在这些情况下，您可以使用一个控制/候选系统，比如 [Scientist](https://github.com/github/scientist) 来监控新旧代码路径的行为。

Scientist 允许您逐步推出变更和重构。如果有任何问题，它还允许您立即启用或禁用新的或实验性的代码。能够逐一关闭可疑的代码路径是一个非常好的工具，可以让你更快地接近真正的问题。

## 7。断路器

断路器允许你很好地使用你所依赖的服务。这些通常是响应性关闭，在紧急情况下保护服务之间的交互。例如，如果您在过去 60 秒内看到的某个服务的 500 个错误的数量超过了某个阈值，则响应性的关闭会自动介入并暂停对那些正在挣扎的服务的任何调用。这给了那些依赖的服务恢复的时间，但也让您的 web 进程不必花费时间调用最有可能失败的服务。

响应关闭比任何监控服务都要快得多。监控服务可能会呼叫您的随叫随到的工程师，这将提示他们去他们的计算机，然后搜索正确的行动手册，最后采取行动。当原始页面被发送给一个人时，你的响应关闭已经开始，你处于一个更好的失败模式。

断路器的工作方式与月度账单报告的工作方式类似。就像每月计费报告继承自父类`Report`一样，计费服务客户端也可以继承自`Client`类，默认情况下，该类会为其子类设置断路器。

## 进一步考虑

您可能需要调查一些额外的注意事项。

第一个是关于可见度。无论它有许多漂亮的图形还是只有一些命令行输出，有一种方法来显示不同的位置，在这些位置存储按钮和开关的状态，并将其合并到一个易于理解的位置，这对事件操作非常重要。真的要考虑要花多少时间才能弄清楚一个开关是否被翻转，因为一般来说，你的开关越花哨、越复杂，它就越有可能成为问题的一部分！

你还应该定期测试这些开关是否真的在工作。它真的有用吗？除非你尝试过，否则你不可能有信心知道它会起作用。

使用上面列出的各种技术，您将需要仔细考虑如何形成这些安全措施，以及在哪里存储它们的状态。有许多选项可用:在关系数据库中，数据缓存层，作为环境变量，等等。如果您认为控制失败的方法是全新的部署，那么您甚至可以在代码中使用配置作为最后的手段。

考虑一下切换开关是否需要接触到可能出现故障的组件。如果该交换机需要访问正在运行的生产服务器，而您无法与该服务器通信，会发生什么情况？如果您不能部署更改，您将如何更改应用程序的行为？如果您有一个不可变的基础设施，这可能意味着环境变量完全不可能处理某些故障情况。我们如此依赖数据库来存储我们的应用程序状态的原因之一是，我们非常确信我们可以保留对数据库的访问，以便手动运行 SQL 语句来切换这些保护措施。

归结起来就是:你让你的应用程序在运行时越可配置，你就越不相信它会以可预测的方式工作。你测试过某个用户，当被标记为三个特性时，是如何与你所有的服务交互的吗？当你实现这些旋钮和按钮时，记住你*是在*用知识换取控制权。然而，这仍然是一个更好的交易。对缓解应用程序中的问题进行更多的控制，比自信地知道应用程序崩溃的确切和特定方式，但却没有办法做任何事情要好。