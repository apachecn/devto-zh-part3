# 嘿，开源维护者们！通过持续集成为您的贡献者提供支持

> 原文：<https://dev.to/circleci/hey-open-source-maintainers-empower-your-contributors-with-continuous-integration-g8p>

[![OpenSource_1.jpg](img/63865c1bf5b553b6796168c0f8e82240.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5HaxHiQ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://circleci.com/blog/media/OpenSource_1.jpg)

令人惊讶的是，许多[开源](https://circleci.com/docs/2.0/oss/)项目并没有实现一个[持续集成](https://circleci.com/continuous-integration/) (CI)解决方案。也许你的项目就是其中之一。你可能会想，“我们不需要它，我们知道我们在做什么，”甚至，“我们太小了，我们以后再实现它。”

当维护人员使用 CI 时，它通常是一个不充分的实现，比如在没有任何测试的情况下使用它进行部署。

我应该知道；我自己也为此感到内疚。

但是我想告诉你的是，为一个[开源](https://circleci.com/docs/2.0/oss/)项目实施 CI 的决定并不是关于你的。这是关于提供合适的贡献工具。它是关于用健康的代码使你的项目稳步增长。

在您的[开源](https://circleci.com/docs/2.0/oss/)项目中使用 CI 将有助于授权您的贡献者更容易地制定更好的 PRs，并帮助您的项目发展壮大。

请继续阅读 CI 赋予您的[开源](https://circleci.com/docs/2.0/oss/)贡献者权力的四种方式:

## 1。CI 将实际运行测试

这似乎是显而易见的，但值得一提。投入时间思考项目测试的维护人员需要向潜在的贡献者传达这一点。

怎么会？你可以提醒人们进行测试:

1.  在`README.md`
2.  在`CONTRIBUTING.md`
3.  或者在[拉式请求(PR)模板](https://help.github.com/articles/creating-a-pull-request-template-for-your-repository/)中

但是，即使采取了这些步骤，有多少人会真正阅读它们并坚持到底呢？用 CircleCI 这样的 CI 提供者运行项目测试意味着您不必担心贡献者懒惰或健忘。这是项目代码质量的安全网。

## 2。CI 构建失败是正常的；把它们作为一个教学点

第一次为一个[开源](https://circleci.com/docs/2.0/oss/)项目做贡献可能会令人生畏。让贡献者知道 PRs 是受欢迎的，但更重要的是，他们不需要一次就做对。如果公关状态检查变成红色，太好了！您可以做的第一件事是确保用户知道如何点击 CI 提供者并检查错误(PR 页脚中的测试通过/失败链接)。不要假设任何事情。然后你可以给那个人建议如何解决问题，给他们时间去解决。这里的想法是，这是一个教学点。记住:他们可能不会像你一样以同样的速度消灭错误。贡献者可以学习并变得更强，同时这种互动为你的项目及其社区定下基调。友好相处，一起解决问题。

## 3。CI 允许贡献者从事他们可能无法从事的项目

我将用一个真实的例子来解释这一点。让我们看看 [CircleCI CLI](https://github.com/CircleCI-Public/circleci-cli/) ，这是一个[开源](https://circleci.com/docs/2.0/oss/)项目，为 macOS 和 Linux 编译。如果用户要报告一个特别影响 CLI 的 macOS 版本的错误，我可以尝试修复它。作为一名 Ubuntu Linux 用户，我可以编写代码，甚至可以交叉编译，但我无法实际测试它以确保修复工作正常。我没有苹果电脑。

进入 CircleCI。

我可以用我的补丁打开一个 PR，CircleCI 将在 macOS 机器上构建并测试它。它为我消除了一个技术障碍。同样地，CI 可以减少人们对你的项目公开 PRs 的摩擦。

## 4。不需要为非部署作业构建机密

当 API 令牌、SSH 密钥等。参与了一个构建，[你不希望那些构建秘密落入坏人手中](https://circleci.com/blog/managing-secrets-when-you-have-pull-requests-from-outside-contributors/)。这可以理解。默认情况下，CircleCI 上的公共项目在分叉 PRs 上关闭了秘密。

我们支持你。

你所能做的是努力让分叉的 PRs 达到一个绿色的构建，而不是一开始就有那些秘密。依赖于秘密的命令应该被隔离到它们自己的任务中，或者用 Bash 逻辑阻塞。通过这种方式，外部的 PR 将能够运行通过的测试，并且项目维护人员可以在`master`分支或其他方式中运行集成测试或部署。

## 结论

一个配备了 CI 工具的[开源](https://circleci.com/docs/2.0/oss/)项目降低了投稿的门槛，同时提高了整体质量。开源与人和代码一样重要。被授权的贡献者更快乐、更有效率。这有利于项目，意味着每个人都赢了。

***P.S.*** CircleCI 给所有用户/项目一个免费的构建容器。你知道我们给[开源](https://circleci.com/docs/2.0/oss/)项目，具体来说就是 **4 个容器**吗？还是免费的。[我们一起来做软件吧！](https://circleci.com/signup/)。