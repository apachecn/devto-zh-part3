# 我最喜欢的开发用红宝石

> 原文：<https://dev.to/scottw/my-favorite-ruby-gems-for-development-2aj2>

您可以在项目中使用许多 Ruby Gems。以下是我最喜欢的改善开发体验的方法，主要是在 Rails 应用上。

[Faker](https://github.com/stympy/faker)——生成电子邮件、企业名称等虚假数据。这使得向您的测试数据添加一些多样性变得容易，并且让您少考虑一件事情

[rack-mini-profiler](https://github.com/MiniProfiler/rack-mini-profiler) -面向机架应用的中间件分析器。它做了很多，但是我的主要用例是在给定的请求中保持数据库查询。它隐藏的超能力是，如果你需要帮助追踪一个 bug，在生产中使用它也是安全的。

[撬](http://pryrepl.org/)和[撬杆](https://github.com/rweng/pry-rails)——IRB 的惊人替代方案。几年来我每天都用它，但仍然觉得我只有它 20%的能力。不管是好是坏，我最好的调试是用一系列的`binding.pry`语句。

使两个数据库之间的数据同步变得容易。我的主要用例是获取生产数据的特定子集(组)，以便于本地调试(不需要下载整个数据库)。

使用键盘快捷键(cmd+shift+x ),你可以看到当前页面上使用的布局、视图和部分内容。还有，你也得到了控制器+动作。最后，你可以点击它们中的任何一个，快速跳转到代码和标记。一个警告是它依赖于 jQuery，所以我不能在最近的一些项目中使用它。

每次我开始一个新项目时，我都告诉自己我会坚持传统，不使用 RSpec。30 分钟后，我又回到那里。这绝不意味着 RSpec 比 Rails 内置测试更好。我只是更喜欢 RSpec，它有一个庞大而成熟的社区。

工厂机器人(和[工厂机器人轨道](https://github.com/thoughtbot/factory_bot_rails))——类似于 RSpec，这不是对内置设备的攻击，我发现 API 和工作流更有吸引力。

守卫(Guard)——自动化常见任务，通常与文件更改相关。规格已更改。有 100 多个插件用于各种其他任务。

[Timecop](https://github.com/travisjeffery/timecop) -让它变得简单(ier？)来测试日期:

> 一个提供“时间旅行”、“时间冻结”和“时间加速”功能的 gem，使得测试依赖于时间的代码变得简单。它提供了一个统一的方法来在一个调用中模拟 Time.now、Date.today 和 DateTime.now。

如果我漏掉了其他人，请在推特上通过[hi@scottw.com](mailto:hi@scottw.com)或 [@scottw](https://twitter.com/scottw) 告诉我。