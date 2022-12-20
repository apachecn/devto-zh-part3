# 如何写一篇精彩的 GitHub 自述

> 原文：<https://dev.to/healeycodes/how-to-write-an-awesome-github-readme-2ldc>

我通读了我能找到的最早的自述文件。由 UIC 计算机科学系的威廉·厄尔于 1975 年撰写。大约 44 年后，文本有点枯燥，但也令人惊讶地相关。*“由于编译器中的一个错误，该函数无法正确编译”*。

自述文件反映了如何维护存储库。一个好的项目并不一定意味着一个有完美测试的活跃的、无 bug 的项目。但这暗示了所有者关心的是**你**，用户(或未来的维护者)。一个好的自述文件会告诉你使用项目和参与项目所需要知道的一切。它*销售*项目——但同时也尊重访问者的时间，让他们知道是否需要不同的解决方案。

当使用 GitHub 配置文件作为应用程序的一部分时，READMEs 以不同的方式很重要。他们展示了技术写作技巧——良好沟通和撰写软件相关文档的能力。期望有人深入你的代码而不提供解释项目的抽象是一个相当大的要求。

## 明确描述

应该有人不用读一行代码就能使用你的软件。

首先，改变 GitHub 提供的默认标题——把`python-ml-project-for-cat-lovers-2`变成`Cat Crawler - Classify Cat GIFs`。下一步是用最简单的形式解释你的项目。许多人在顶部使用一行程序。

> 一个下载和索引猫 gif 的机器人

当你在你的销售线索描述中加入更多的内容时，你就增加了认知超载的机会。编辑完语言后，使用标题、换行符和空格来进一步分解它。(两行代表一个新段落，`<br>`代表一个分隔符。[备忘单](https://help.github.com/en/articles/basic-writing-and-formatting-syntax)。)不要回避产品 logos 和截图。与其他技术文档不同，多媒体在这里表现很好。对于更深奥的知识库，背景阅读部分会有所帮助。

如果你的知识库有趣或轻松，那么你的描述应该显示这一点！斯特伦克&怀特有他们的位置，但互联网也是酷程序员发挥创造力的地方。大声喊出来[未付费](https://github.com/kleampa/not-paid)，如果客户不支付他们的费用，网站会慢慢消失，直到在某个日期变得不可见。

## 用法

别人如何使用你的项目？如果是 API，要有一段最基本交互的代码。您的完整文档可以在页面下方或其他位置。facebook/react 用一个例子展示了它们的用法——这是人们可以使用 react 的最小方式。使用单引号来引用代码，在代码块的新行上使用三个反引号。将语言放在前三个反勾号之后，以便突出显示。

```
function HelloMessage({ name }) {
  return <div>Hello {name}</div>;
}

ReactDOM.render(
  <HelloMessage name="Taylor" />,
  document.getElementById('container')
); 
```

显示此交互的输出。如果你能以任何方式使用 GIF，那就这样做吧！gif 对于人们来说很容易解析，并且它们向你的读者传递了大量的数据。alexfoxy/laxxx ，一个平滑网页动画库，完美地做到了这一点。

[![alexfoxy/laxxx](img/53a2e19285f71dfe2e2d9a9db4ebe4f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C5KJqyzd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mt2c6r3povfwc0g7bo7f.gif)

我使用开源工具 [ShareX](https://getsharex.com/) 来创建我的 gif。就像选择屏幕上的一个区域一样简单。我推荐的另一个解决方案，也是开源的，是 [LICEcap](https://www.cockos.com/licecap/) 。

## 安装

希望在看到你的项目运行后，你的访问者想要安装它。这一部分有时被称为*入门*。每个项目都应该有这个，哪怕只是`npm install catcrawler`。如果这是一个静态网站，那么就说出来！`host the parent dir on a webserver`。假设了解基本工具。你不需要解释什么是`pip`或`npm`，但是你应该列出你将在新的设置上运行的每个命令。

[DEV](https://github.com/thepracticaldev/dev.to/#getting-started) 有一个完整的章节，用于构建和运行，如果你的目标是可访问的，这是一个很好的模型。启动虚拟机并尝试复制您的安装指南是一个很好的做法——假设您没有自动构建测试来为您做这件事。

## 徽章

[![example badge](img/5e48451338d9daadc7db8d145dffdad2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KXOV4Nm---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/03c8ps5uxu9e5m3axekr.png)

GitHub 徽章大多由[徽章/盾牌](https://github.com/badges/shields)标准化，是访问者向下滚动时首先看到的东西之一。构建状态徽章描述了项目的稳定性。其他标记可能显示每月提交的存储库的活动，或者维护者的数量。它们不是强制性的，但很像 gif，它们是一个巨大的奖励。

shields.io 有一个创建你自己徽章的 API，他们也有一个 npm [包](https://www.npmjs.com/package/gh-badges)很好用。几周前我拿起它，在不到一个小时的时间里就有了一些徽章！另一个 npm 替代品是[獾](http://badges.github.io/badgerbadgerbadger/)。Python 有谷歌的 [pybadges](https://github.com/google/pybadges) 。

如果你想获得你的第一枚`build: passed`徽章，我在 DEV 上写了一篇[文章](https://dev.to/healeycodes/earn-a-build-passing-badge-on-github--testing-your-express-app-with-travis-ci-1m2f)，带你开始 GitHub 上的持续集成。

## 投稿

如果你正在寻找他们，一个贡献者的部分是有帮助的和受欢迎的。有一个 GitHub [标准](https://help.github.com/en/articles/setting-guidelines-for-repository-contributors)用于添加一个`CONTRIBUTING.md`文件到根目录。这可能包括行为准则以及发现问题和构建拉动式请求的一般准则。许多初学者对帮助开源项目的过程感到焦虑，一步一步地引导他们可以缓解这种焦虑。如果你不确定从哪里开始，我最近看到了一个行为准则[生成器](https://github.com/uyouthe/code-of-conduct)，我认为它非常简洁。我知道我的一些朋友将只支持对维护者交互有严格指导方针和规则的存储库。

## 执照

当我在工作中寻找解决方案时，执照是我首先要找的东西。当通过 GitHub 创建一个库时，有一个选择许可证的选项，这会为您生成一个`LICENSE.md`文件。GitHub 在这个文件上也有一个[页面](https://help.github.com/en/articles/licensing-a-repository)——他们是[choosealicense.com](https://choosealicense.com/)的创建者，一个带你浏览所有选项的奇妙向导。我个人使用麻省理工学院的开源代码。有些人对许可有强烈的意见，尤其是当涉及到 [GPL](https://tldrlegal.com/license/gnu-general-public-license-v3-(gpl-3)) 的时候。*“对软件的任何修改，包括(通过编译器)GPL 许可的代码，也必须在 GPL 下与 build & install 指令一起提供。”*

## 模板

[那里](https://github.com/RichardLitt/standard-readme) [那里](https://github.com/zalando/zalando-howto-open-source/blob/master/READMEtemplate.md) [那里有许多](https://github.com/noffle/common-readme)自述模板。它们在紧要关头很棒，但我发现较小的项目不太适合这种盒子，结果文本看起来有点冷。成熟的项目从模板中获益更多，但是考虑到开发人员可能投入软件的时间，拥有一个定制的解决方案是值得的。

这个是我最喜欢的，因为它直截了当，并且有两个测试小节。如果您有任何测试，这应该在您的自述文件中提及。当克隆一个项目时，运行测试是我做的第一件事，以确保我的设置为开发做好了准备。

## 其他章节

一旦你看完了所有重要的部分，请尽情填写你的自述文件。我可能是少数，但我喜欢浏览 GitHub 寻找新的东西，并发现它们是如何建立的。我喜欢有大量示例代码的详细存储库。对我来说，参与一个项目，重要的是看到维护人员至少和我一样关心。

探索你的编程语言的[趋势](https://github.com/trending),了解标准布局。为了获得灵感，看看我最近最喜欢的两个[盖茨比](https://github.com/gatsbyjs/gatsby)和 [lax.js](https://github.com/alexfoxy/laxxx) 。最重要的是，让你的文档唱歌。

* * *

加入我的关于编程和个人成长的[时事通讯](https://buttondown.email/healeycodes)的 150 多人注册！

我发关于科技的微博。