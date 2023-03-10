# ✨公司的测试基础设施，DevOps 的故事

> 原文：<https://dev.to/sigje/testing-infrastructure-at-corp-a-devops-story-47a9>

大三学生乔治 are 和大四学生索尼娅 are 正在合作进行基础设施迁移的第一阶段。由于他们都远程工作，他们使用 [Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=devto-blog-jedavis)和 [Live Share](https://visualstudio.microsoft.com/services/live-share/?WT.mc_id=devto-blog-jedavis) 进行配对，这样他们就可以协作编辑和调试，同时还能保持他们自己的所有特性和偏好。

他们启动了一个 [Zoom 会议](https://support.zoom.us/hc/en-us/categories/200101697),并在他们的团队频道上发布了联系细节，这样任何人都可以在他们的配对会议期间加入。

“我已经在我的本地机器上克隆了 [packer](https://www.packer.io/) repo，”乔治一边用 VS Code 打开文件夹，一边说，他知道索尼娅会同意她的观点。“让我们从一个功能分支开始。我们应该以[项目委员会](https://help.github.com/en/articles/about-project-boards)的发行号命名，对吗？”

“太好了，”索尼娅说。“那太好了。”

“所以我在这里拿了现有的 [JSON 模板](https://www.packer.io/docs/templates/index.html)。我去掉了所有额外的定制，做了一个基础模板。我们可以重复使用这个基础模板进行定制，而不是复制一个定制的模板，”George 解释道。

“这是一个伟大的想法，”索尼娅说。“让我们继续并提交该更改，这样我们就有了[个单独的提交(如果需要的话)](https://www.atlassian.com/git/tutorials/cherry-pick)”

“你知道，我想知道我们是否现在编写一些 [InSpec](https://www.inspec.io/) 代码来测试我们的基础设施，然后我们可以使用这些相同的测试来验证我们在云提供商实例上的应用。这样，无论应用程序是在本地还是在云中，我们都可以增强应用程序正常工作的信心，”George 评论道。他把手从键盘上拿开，直视屏幕，暂停编码。

“我没听说过 InSpec。什么事？”索尼娅问道，切换到相机视图，因为她想把重点放在乔治说什么。

“这是一款针对基础设施的测试工具，主要宣传为一种实现代码合规性的方法，它完全可以做到这一点，但我发现用它来测试我的基础设施非常有用，只是为了确定我是否按照我预期的方式设置了一些东西。例如，如果我正在设置一个 http 服务器，我可以使用一个 [InSpec http 资源](https://www.inspec.io/docs/reference/resources/http/)来验证一个特定的端点。因此，就像开发人员为他们的代码编写单元和集成测试一样，我们也可以为我们的基础设施编写测试，”George 解释道。

“这听起来很有趣，但我担心我们会花很多时间来评估这些选项，以及在不增加项目计划开销的情况下，这些选项在短期内对我们是否有用，”索尼娅说。虽然这个想法听起来很吸引她，但团队中没有太多多余的能力来支持学习新工具。

“嗯，当我开始从事这个打包项目时，我做的事情之一是在打包程序构建操作之后添加一个 InSpec 验证操作，然后……”乔治开始说。

“啊哦，然后呢？”索尼娅问。

“嗯，对我来说，非常有帮助的是，明确地陈述了我对图像在构建时应该包含的内容的期望……”

“但是，如果你把 InSpec 添加到图像中，难道不会改变与现有依赖项的潜在交互吗？”Sonia 打断道。

乔治摇着头解释道。不，它在构建的外部运行，这样我们可以在发布图像之前验证图像

“哦，那相当有趣。因此，我们可以消除构建中的一些已知问题，并有一种方法来收集我们已经在验证的信息。哦！因此，安全工程部门的 Erin 也可以直接查看回购，并了解我们正在验证的内容。这也可能会简化我们进行安全审计的方式，”索尼娅说。尽管安全审计通常需要几天时间，但她对时间表的时间限制很敏感。放大这个项目每个部分的那些日子，以及手动检查中节省的时间，它可能会使花时间学习一个新工具变得值得。

"我们能先看一下你目前掌握的情况吗？"她问。

乔治切换回 VS 代码，调出`profiles`目录，打开一个`default_spec.rb`文件。“这只是针对映像运行的默认测试集。现在它是非常基本的。我让它测试默认用户是否仍然存在于映像中，以及我们是否可以访问系统。它还检查以确保 NTP 正在运行，因为我们的一些服务对时间很敏感。声明一个测试的方式是以描述语句的形式:

```
describe service('service_name') do
  it { should be_installed }
  it { should be_enabled }
  it { should be_running }
end 
```

Enter fullscreen mode Exit fullscreen mode

因此，对于我们的 NTP 服务，我有:

```
describe service('ntp') do
  it { should be_enabled }
  it { should be_running }
end 
```

Enter fullscreen mode Exit fullscreen mode

如果有任何失败，图像不会加载到存储库中。有很多不同的资源。这是 ruby，所以创建复杂的资源并不难，”乔治热情地说。

“这真是太棒了。让我们与 SRE 团队的其他人分享这一点，因为我认为采用与现在不同的方式测试我们的基础设施对我们来说非常有用。这看起来就像我们当前用来验证实例的 shell 脚本可以被这个 even 所取代！

索尼娅说:“我们暂时不要把它添加到知识库，直到你有机会在每周团队会议上分享这一周的学习成果。

“哦不！我还不能分享这个，它还不完整。”乔治说。

“它完全可以被分享。我认为现在我们需要从团队那里获得更多的反馈，看看他们对此有什么看法。”索尼娅说。

“我认为那次会议更多的是为了炫耀人们这一周的成就，”乔治说。

“不。我承认有时看起来是这样，但实际上，这是一种在团队中分享新想法和传播知识的方式。我们不可能都是专家，也不可能什么都懂。但这是一种尽我们所能传播知识和理解的方式，也鼓励人们像你在这里所做的那样去尝试。通过尽早分享这种可能的工具和流程变更，我们可以防止人们对引入现成的变更感到惊讶。”索尼娅解释道。

“这是一个很好的观点，我没有这样想过，”乔治说。

“我将更新本周的团队会议议程，您可以在周三分享您的工作。Sonia 说:“让我们继续检查基础映像的打包程序更改，这样我们就可以获得一个拉取请求。

在审查之后，他们意识到在将提交集成回中央共享存储库之前，需要进行一些更改。

```
git commit -m "Copied customized template to a new base template.

This introduces a new base template that can be used to build 
a base Sparkle Corp image with all the security mandated packages, as well as monitoring and minimum version of shared common packages.

This new base template can also be used as a starting point for new customized images.

Co-authored-by: george <george@sparkle.corp>
Co-authored-by: sonia <sonia@sparkle.corp>" 
```

Enter fullscreen mode Exit fullscreen mode

“很好，提交解释了您所做更改的背景，以及原因和方式。这样我们可以回顾过去，知道我们在这里想什么，”索尼娅说。

在索尼娅的鼓励下，乔治向他的团队展示了一个关于 InSpec 的简短教程，同时还展示了他对封隔器底座图像的样本测试。SRE 团队中的每个人都非常兴奋，轮流与乔治搭档，开始为每个不同的服务创建个人资料。由于团队已经使用[厨师长](https://docs.chef.io/chef_overview.html)来管理他们的[基础设施，就像代码](https://docs.microsoft.com/en-us/azure/devops/learn/what-is-infrastructure-as-code/?WT.mc_id=devto-blog-jedavis)一样，测试资源的映射感觉非常无缝。此外，该团队可以利用社区中可用的[合规性配置文件](https://supermarket.chef.io/tools?type=compliance_profile)进行更严格的安全测试。

将这些测试插入到他们的持续集成和持续部署系统中，使得 SRE 团队能够加快他们正在构建的基础设施的测试，并增加对系统配置正确的信心。