# 使用 Ruby 的 Slack Bot

> 原文：<https://dev.to/caleb_mcquaid/slack-bot-with-ruby-44pf>

我和另外四个人是终生的朋友。我最近说服他们都加入了一个 Slack 频道(其中一个是 Android 用户，因此出现了可怕的绿色信息)。懈怠很棒。我是一名新的开发人员，我们已经在我工作的最后两个地方使用了它。Slack 的一个很大的特点是可以安装大量的应用程序和机器人。我正在尝试学习后端编程，我是 Ruby 和 Ruby on Rails 的忠实粉丝。Ruby 对开发者非常友好，当你克服了它最初的怪癖后，它会变得非常棒。

堡垒之夜是我们一起度过时间的主要方式之一，我们总是在查看我们什么时候能聚在一起玩。所有这些最终导致了用 Ruby 编写 Slack bot 的想法。这个机器人允许我们任何人问机器人“谁在线？”。机器人将进入 Xbox Api，检查这 5 个人的状态，并报告他们是否在玩堡垒之夜。它的功能非常简单。它是用纯红宝石打造的。不需要导轨。需要一些外部设置，但是实际的程序具有最小的外部规格。

所以让我们开始吧！

设置

正如我所说的，我使用了纯 Ruby，所以我从一个 gem 文件和一个主文件开始，我将它们命名为 slack.rb。

slack-ruby-bot——这颗宝石对应用的成功至关重要，因为它是应用和 Slack 之间的纽带

Xbox-api——Xbox 没有 Ruby 特有的 API 实现。他们确实有一个面向所有 C++爱好者的 C++实现。我用的是艾伦·韦恩提供的非官方 Xbox API。文档很棒，他提供了 Python 和 Ruby 的不同实现。

json——JSON 在处理大多数 API 时非常关键，这个宝石允许 Ruby 和 JSON 很好地合作。

dotenv —最后但同样重要的是，我使用 dotenv 来隐藏我的 Slack 和 Xbox Api 密钥。

将您的 gem 添加到 Gemfile 之后，请确保在您的终端上运行 bundle install 来填充您的 Gemfile.lock 文件。外部设置需要你从 Slack 和 Xbox API 中获取一个 API 密钥。这两个关键都是相当直接的过程。

Xbox Api

在我们深入讨论之前，讨论一下这个 api 如何获取玩家数据是很重要的。api 使用玩家的 accountxuid。我没有添加一个单独的调用来获取每个玩家的 id，而是提前做了一些繁重的工作。我用 Postman 向 Xbox Api 发送了一个带有我朋友玩家标签的请求(具体来说是 GET[https://xboxapi.com/v2/xuid/{gamertag}](https://xboxapi.com/v2/xuid/%7Bgamertag%7D))。确保用 X-AUTH 和 api 键设置您的头。)Postman 然后吐出他们的 xuid，最后我把这个硬编码进程序里。我的想法是，如果没有额外的 api 调用，程序的性能会稍微好一点。

编码

回到你的 slack.rb 文件。要求所有的 gem 都在文件的顶部是很重要的，这样 Ruby 就知道要导入什么了。然后，我让我的类成为 Slack Ruby Bot 的一个实例。之后，我把我的 api 键放入一个名为“client”的变量中，当我为我所有的朋友设置变量时，我使用“client”。一旦这些都设置好了，就到了松弛实现的时候了。Slack 使用一个名为“命令”的函数来提供功能。这可以在 Slack Ruby Bot 自述文件中得到更全面的解释。我使用单词“pong”作为我的机器人的 Slack 关键字。这个关键字将使机器人开始获取数据，你可以把它改成你想要的任何关键字。

接下来是功能性。我将玩家数据放入变量中，从 Xbox API 中获取他们的“存在”并将其设置为一个变量。你得到的数据是一个 Ruby 对象(我想是 _(ツ)_/)。我花了大部分时间在这个项目上，寻找一些不用进入 JSON 领域就能解析这些数据的方法。可惜我没有成功。在获得这些 Ruby 数据之后，我使用 JSON gem 来获得其余的数据。我对每个新变量调用 JSON.generate，并将其设置为另一个变量。然后我调用 JSON.parse，现在我在 JSON 的土地上，我可以取出他们正在做的事情的数据。if 语句根据玩家数据决定将什么打印到 Slack。在这之后，保存所有的东西，然后移动到你的终端，把 cd 放到项目中。在那里，运行以下脚本:

SLACK_API_TOKEN={SLACK API KEY}捆绑包 exec ruby slack.rb

这将在您的本地计算机上启动您的应用程序。我花了很长时间试图把这个推给 Heroku，但没有成功。还在努力。转到您的 Slack 客户端，输入以下内容:@{SLACK BOT NAME} {YOUR COMMAND}稍等几秒钟，viola！信息！

差不多就是这样！最后一点:

这肯定不是非常高的性能，肯定有更好的方法来做到这一点。这正是我要做的。让我知道什么适合你！

Xbox API 免费层每小时只允许 120 个请求。在开发过程中，我遇到过几次这种限制。付费层级不太贵。

在 Github 上查看我的代码，看看我是如何设置的。感谢您查看我的项目！很想听听大家的经历和想法！祝游戏愉快！