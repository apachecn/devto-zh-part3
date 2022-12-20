# Meetup 关于日本创业公司 React 本土用户的报告第 3 期。

> 原文：<https://dev.to/sasurau4/meetup-report-about-react-native-users-in-japanese-startups-the-3rd-14l>

## [T1】简介](#intro)

我参加了 2019 年 3 月 28 日日本创业公司 React 原生用户的活动。这个 me7 的人谈论了一些本地的东西，比如它的优缺点或者使用上的困难等等。
etup 是第三届，在东京举办。https://r-n.connpass.com/event/121961/
T3

7 个人在 5 到 10 分钟内谈论了关于 React Native 的各种事情，比如它的优点和缺点或者使用上的困难等等。

虽然所有的 lightning talks 和它的幻灯片都是日文，但是很激动。所以，我用英文发布了关于 meetup 的报道，因为我想帮助 react 本地社区。
日本有很多 React 原生用户！

## 注

以下摘要原本是日文，所以我的翻译可能是错误的。如果有错误，那也不是主持人的错误，可能是我的错误。

## LTs 汇总

### 基于 react web app 启动 React 原生 app。

主讲人:@t0m0120
幻灯片:[https://speaker deck . com/t0m 0120/react zhi-websabisuworeactnativedeapurihua-Sita Hua](https://speakerdeck.com/t0m0120/reactzhi-websabisuworeactnativedeapurihua-sitahua)

演示者谈到了他们推出基于 react web app 的新 react 原生应用程序的速度。
[socialdog](https://social-dog.net/en) 是一项让我们智能高效的 Twitter 账户管理服务。
他们仅用 4 个月时间就推出了基于 react web app 的新 iOS 应用。
为什么发展这么快？答案是他们在 web 和本地使用相同的逻辑。
最好的例子就是 redux code。演示者说我们的代码和 react 和 react native 中的代码几乎一样！

你认为这是 React 核心理念之一的`Learn Once, Write Anywhere`的成功吗？
我觉得绝对可以。

### 我想谈谈 react-native-firebase 的许多陷阱

主讲人:@tkow39
幻灯片:[https://www . slide share . net/takeku sama/react-nativefirebase-startup mtup-138554329](https://www.slideshare.net/TakeoKusama/react-nativefirebase-startupmtup-138554329)

演讲者谈到了 react-native-firebase 库是多么有用。但是由于它的各种功能，也有许多缺点。演示者讲述了他陷入困境的案例。更糟糕的是，React Native 提供的名为 react-native-git-upgrade 的工具还没有得到维护，并且运行不正常。认证、DeepLink、Firestore 等陷阱。

我认为一些陷阱是由火基本身引起的，而不是反应原生火基。当然，firebase 和 react-native-firebase 都很棒，也很有用！我希望有人谁 stucks 在陷阱分享知识或解决办法，甚至有助于官方文件！

### React Native 中的可测性

主讲人:@L_e_k_o
幻灯片:[https://talks.leko.jp/react-native-testability/](https://talks.leko.jp/react-native-testability/)

演讲者谈到了如何为 React 本机应用程序编写测试。
React 原生应用一般由 redux、usecase、universal JS、native layer 等多个部分组成。他介绍了一些有用的写作测试技术和库。比如界面注入，使用[重选](https://github.com/reduxjs/reselect)、【超级测试】()和[故事镜头](https://www.npmjs.com/package/@storybook/addon-storyshots)。为了不需要编写 E2E 测试，保持连接逻辑尽可能简单。他想知道如何为一些本地模块编写测试，比如蓝牙和推送通知。它们在模拟器中不可用。你知道如何测试它们吗？

### 升级 React Native 没有捷径。

主讲人:@natural_clar
幻灯片:[http://slides.naturalclar.com/](http://slides.naturalclar.com/)

演讲者谈到了升级为什么以及如何对本机做出反应。
原因很简单。升级让我们使用像 React Hooks 这样的新功能！
但是升级偶尔会破坏我们的应用程序，例如中文和日文的文本输入被破坏，构建失败是因为 XCode 10 新的构建系统。
升级 React Native 需要许多步骤，如升级 npm 依赖项、更改构建配置(build.gradle 或。pbxproj 文件)等等。
更糟糕的是，React Native 提供的名为 react-native-git-upgrade 的工具没有维护，也没有很好地工作。
但是名为 rn-diff-purge 的新工具解决了这种情况。通过使用这个工具，我们可以得到每个版本的差异。因此，将这些差异与我们的项目进行比较并加以应用，可以让我们轻松升级 React Native。

### React Native 和 GraphQL 开发的有用性

主讲人:@mediaboxes
幻灯片:[https://speaker deck . com/media boxes/react-native-x-graph QL-dekai-fa-sitarabian-Li-datu Taohua](https://speakerdeck.com/mediaboxes/react-native-x-graphql-dekai-fa-sitarabian-li-datutaohua)

演讲者谈到了 React Native x GraphQL 的优缺点。他解释了 GraphQL，并谈到了他使用的 Apollo 客户端。他得益于 apollo 的缓存系统，可以自动更新缓存，无需 Redux。他解释说缺点也是缓存控制和错误处理。

我之前用 React Native 和 GraphQL 的时候觉得 React Native 和 GraphQL 的结合非常好。阿波罗客户端有许多功能，所以使用它们有点复杂，但它非常有用！

### 挣扎着引进 E2E 试验库排毒

主讲人:@ariiyu
幻灯片:[https://speaker deck . com/arii Yu/fight-with-introduction-of-e2e-test-library-detox](https://speakerdeck.com/ariiyu/struggle-with-introduction-of-e2e-test-library-detox)

演讲者谈到了排毒和它的麻烦拍摄。
[Detox](https://github.com/wix/Detox) 是 React 原生 app e2e 测试的好库。
他遇到了 E2E 试验没有结束的麻烦。他一头扎进排毒的工作原理，一步一步稳步调试。最后，他找到了原因。默认情况下，排毒等待动画结束，应用程序中有背景无限动画循环。因此，他通过仅在 E2E 测试时替换文件来解决这个问题。
他完成了 LT，说他想让戒毒伙伴分享知识。

### 一个 web 工程师如何在 React Native 中战斗

推荐者:@NaoshiHoshi
幻灯片:[https://www . slide share . net/NaoshiHoshi/how-web-engineer-fight-in-the-the-the-react native-138559060](https://www.slideshare.net/NaoshiHoshi/how-web-engineer-fight-in-the-reactnative-138559060)

演讲者谈到了一个 web 工程师如何在 React Native 中战斗。他的公司遇到了困难，有 iOS 应用程序，却没有本地应用程序工程师。主要开发人员已经离开了他的公司，剩下的工程师没有本地应用程序的开发经验。他们无法继续开发现有的 iOS 应用程序。
于是，他们改变了方式，改为选择 React Native。他决定只在 React Native + Expo 中实现功能。未弹出。

我认为他的想法是正确的，因为 React Native 由于跨平台而有一些限制。避免这些约束是使用 React Native 进行开发的关键。

## 其他

meetup 组织者每次都尝试非常有趣的挑战。这时，有座位，申请人必须带香槟到会场。我们在聚会开始时喝了香槟！组织者宣布他正在计划一个大约有 100 人参加的大型聚会。
我很期待！

## 结论

在日本有许多 React 本地用户及其社区。React Native 是一个很好的框架和令人兴奋的工具。感谢维护者和社区！

感谢阅读！