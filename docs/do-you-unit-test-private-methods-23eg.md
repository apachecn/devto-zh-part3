# 你对私有方法进行单元测试吗？

> 原文：<https://dev.to/dan_mcm_/do-you-unit-test-private-methods-23eg>

最近在工作中遇到了一个有趣的问题，那就是对一个新的回购协议进行单元测试。讨论中的 repo 相对较小，功能类似于一个基本的 cronjob，但是为了遵循一些最佳实践，我决定加入一些基本的单元测试。

在这样做的时候，我错误地为用标准下划线前缀表示的私有 Python 类编写了单元测试！因为我已经有一段时间没有用 Python 编码了，所以我已经忘记了这个语法，但是当我从一个同事那里听到最佳实践似乎是有目的地不测试私有方法时，我有点吃惊。

我很想听听开发者社区对此的看法？我从它们的本质上理解，你可能应该选择端到端地测试方法所属的类，而不是选择单元测试，但是当你及时地寻找简单的测试功能时该怎么办呢？

[![](img/ae48592b7aad6a5fd998f6fe33eca6fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZQe0D4K8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ekiy5aot90-flywheel.netdna-ssl.com/wp-content/uploads/2014/10/segue-blog-benefits-unit-testing.png)

很想听听社区对此的看法！(跨不同的语言/框架——不仅仅是 Python)