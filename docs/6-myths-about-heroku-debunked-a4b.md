# 关于 Heroku 的 6 个神话被揭穿

> 原文：<https://dev.to/heroku/6-myths-about-heroku-debunked-a4b>

说到 Heroku，误解和神话比比皆是。许多人对 Heroku 有所了解，他们听说过它很容易使用，或者是一种无需干预的应用部署方式，但除此之外，他们并不清楚 Heroku 到底能做什么。让我们揭穿六个最常见的 Heroku 神话。

## 1: Heroku 不缩放

Heroku 易于使用，安装快捷。正因为如此，这个平台经常被创业公司和开发小型个人应用的个人所使用。许多人认为 Heroku 只对小项目有用，但事实是 Heroku 可以和你一起扩展。我们将它设计为即使在扩展时也能保持易用性。Heroku 有一个长长的客户名单，像金融时报、丰田和梅西百货这样的公司已经使用 Heroku 取得了很好的效果。Citrix 的 GoToMeeting 服务建立在 Heroku 之上，并使用 [Heroku 附加组件](https://elements.heroku.com/addons)，包括 [New Relic APM](https://elements.heroku.com/addons/newrelic) 来监控和调整他们的应用程序，以及 [Logentries](https://elements.heroku.com/addons/logentries) 来分析任何当前问题和性能。

对我来说，最好的例子是 ipify，这是一个简单的服务，到处都有大量的服务在使用。像许多好的想法一样，ipify 首先在 Heroku 上面向公众，但是 Heroku 也能够很容易地扩展到每天处理数百万个请求。以这种方式，Heroku 经常看起来好得难以置信，许多开发人员错过了在他们更大的项目中实现 Heroku 可以节省大量时间和精力的许多方法。

## 2: Heroku 不能给你精细的控制

的确，Heroku 自动化程度很高，消除了许多部署应用程序的繁重工作，但这并不意味着你被锁定在一条道路上。在 Heroku，我们处理尽可能多的工作，以便您可以专注于更重要的任务，但随着您的服务增长，总会有一些边缘情况，您会希望收回控制权。

IP 过滤就是一个很好的例子，在许多其他平台上，IP 过滤要么是自动设置且不可更改的，要么是每次都需要配置。使用 Heroku，你可能会从一个基本配置开始，使你的 dyno 通常相当公开，但使用 [Heroku Private Spaces](https://devcenter.heroku.com/articles/private-spaces) ，你可以严格控制谁可以从哪里访问你的服务，并完成 IP 白名单。还有多个插件可以让你[为你的 Heroku 应用程序修复一个静态 IP](https://devcenter.heroku.com/articles/fixie) 。

尽管 Heroku 在应用程序部署中做了很多繁重的工作，但这并不意味着你会被我们系统的默认假设所束缚。深度配置和附加生态系统意味着 Heroku 可以根据您的团队需要进行定制。

## 3:好用，所以一定不健壮

Heroku 的简单可能具有欺骗性。我们经常把简单和易用与缺乏功能联系在一起。不幸的是，伟大的用户体验设计非常罕见，以至于许多潜在用户认为 Heroku 易于使用意味着它缺乏深度。但是 Heroku 既强大又好用。我们不干涉您的工作，并在后台处理您不想管理的一切，因此您可以继续做更重要的工作。除了在可靠性方面的强大服务，我们还必须考虑我们在多大程度上免受监管或法律问题的影响，Heroku 在整个服务中坚持遵守 [PCI、HIPAA、ISO 和 SOC 合规性](https://www.google.com/url?q=https://www.heroku.com/compliance&sa=D&ust=1552334393214000&usg=AFQjCNHeQkTi3t3i2IFrNVMslZxpUTEB0g)。

想要证明它的强大吗？Heroku 也用 Heroku。我们会尽全力追查赫罗库。因此，我们使用与您相同的工具和功能。我们和我们的客户有着相同的体验。[这就是为什么 bug 修复的很快](https://blog.heroku.com/bug-bounties-black-swans)而且功能不断磨练。我们相信卓越的用户体验设计应该是标准的。

## 4: Heroku 健壮，一定很难上手

正如我们经常听到 Heroku 太容易使用而不是一个健壮的工具的神话一样，我们也听到因为它是一个如此强大的工具，所以它对于初学者来说一定是一个困难的平台。那根本不是真的。Heroku 的设计让任何人都可以开始使用。此外，虽然许多品牌雇佣客户支持代理并向他们传授一些产品知识，但我们的[支持](https://www.heroku.com/support)团队是由受过支持培训的知识丰富的开发人员组成的。

我们的[开发中心](https://devcenter.heroku.com/start)还包含分步指南，可以带你入门和部署你的第一个应用。

## 5: Heroku 不允许你定制或管理你的堆栈

仅仅因为 Heroku 使它快速而容易地启动和运行，并不意味着这个平台缺乏复杂性和功能。Heroku 丰富的功能集让你可以部署、运行和管理用 Ruby、Node.js、Java、Python、Clojure、Scala、Go 和 PHP 编写的应用程序。你可以决定是启用 GitHub 集成还是使用 Heroku API 来构建和发布。Heroku 让您决定哪些要自动化，哪些要手动管理。你可以通过使用[定制构建包](https://elements.heroku.com/buildpacks)进一步定制你的应用程序，如果你需要完全定制，使用 [Docker](https://devcenter.heroku.com/articles/container-registry-and-runtime) 。

## Heroku 是专有的，你被锁定了

在 Heroku，我们相信开源软件的力量和重要性——你在 Heroku 所做的工作是你自己的，如果你需要的话，你应该可以轻松地把它带走。特别是，[如果你很好地设计了你的应用程序](https://12factor.net/)，我们不要求你做任何特别的事情，或者使用任何专有的 API，来让你的应用程序在 Heroku 上运行。如果您决定需要将您的数据文件从 Heroku 上移走，那么在其他地方进行备份和恢复会非常简单快捷。我们对我们的平台和我们提供的服务充满信心，我们不需要锁定我们的用户。

## 潜得更深

我们在 PluralSight 上有一个完整的视频课程，将 Heroku 作为一个平台，并向您展示如何使用 Heroku 更快地完成常见任务。