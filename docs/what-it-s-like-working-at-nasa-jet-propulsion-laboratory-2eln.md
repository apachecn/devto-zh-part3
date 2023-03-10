# 在美国宇航局喷气推进实验室工作是什么感觉

> 原文：<https://dev.to/johnphamous/what-it-s-like-working-at-nasa-jet-propulsion-laboratory-2eln>

有很多关于在大型科技公司工作的传言，但在像美国宇航局喷气推进实验室(JPL)这样的宝石公司工作的却不多。对大多数人来说，他们为能在一个地方工作而神魂颠倒，因为那里有“惊人的”额外津贴，如免费食物和乒乓球桌。在 JPL 国家航空航天局，人们在那里不是为了额外津贴，他们在那里是因为他们真正热衷于了解太空和地球上有什么。这导致了一种重视独创性和探索的文化。

## 我是谁？

我目前在 JPL 工作，开发先进的多任务操作系统，做前端工作，以前是实习生。通过这篇文章，我希望激发你对空间技术的兴趣。

## 什么是 NASA JPL？

[![The Center of the Universe](img/6a012916dbec3c512efccc72901e0755.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_HOIx8Ek--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.nextgov.com/media/img/upload/2014/06/30/7720328304_26c2f8cd96_k/860x394.jpg)

JPL 是加利福尼亚州 La Cañ ada Flintridge 的一个研究机构，执行空间和地球科学任务。几个值得注意的成就是:开发美国的第一个地球轨道科学卫星，开发第一个星际飞船，并发送机器人到其他星球！

JPL 是一个联邦资助的研发中心，由加州理工学院为美国宇航局管理。这使得 JPL 与其他 NASA 中心相比处于一个独特的位置，因为它允许 JPL 在 NASA 范围之外推动创新。当你在 JPL 工作时，你不是 T2 的公务员，而是加州理工学院的私人雇员。

JPL 的历史真的很迷人，我鼓励你多去了解它。

## JPL 做什么工作？

[![Bunnies working on Rover](img/7af75e8e379382f1dbdbc98ac29fb587.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SMQ8316b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/b8a5Ej5.jpg)

这里正在做大量的工作。范围从[平面设计](https://www.jpl.nasa.gov/visions-of-the-future/)、[人工智能](https://www.theverge.com/2018/11/13/18091448/nasa-ai-autodesk-jpl-lander-europa-enceladus-artificial-intelligence-generative-design)、[混合现实](https://www.jpl.nasa.gov/news/news.php?feature=7249)到[折纸](https://exoplanets.nasa.gov/resources/2166/origami-starshade)。在这里工作，你不必非得是火箭科学家。在最近的一次活动中，我遇到了伊丽莎白·兰道，她的官方头衔是“高级说书人”，这太酷了！

## 科技栈是什么样的？

这篇文章的其余部分将从一个前端工程师的角度来写。

你可以把 JPL 的任务想象成传统科技公司的产品。任务经历了从构思到实施的产品生命周期。每个特派团都有要求，这些要求通过定制服务或扩展多特派团服务来满足。最近有一股强大的推动力来构建更多的多任务服务，这可以从重新实现和测试共享服务中节省当前和未来任务的大量时间。

我们使用 Git/GitHub 进行版本控制，大多数任务都在转向 monorepos。

许多任务软件已经从桌面应用程序转移到网络应用程序。

### 前端

*   棱角分明，使用 Typescript(不需要支持 IE，因此我们可以使用最新的浏览器功能！)
*   用于可视化的 D3 和画布
*   Jest 和 Jasmine 用于单元测试
*   E2E 试验用硒

### 后端/开发操作系统

*   Java Spring，Express with Typescript
*   用于 Java 测试的 JUnit 和用于类型脚本测试的 Mocha/Chai
*   Docker(部署在*nix OS 上的大多数服务)
*   詹金斯
*   MongoDB，MySQL，ArangoDB，Dgraph

### 其他

*   测试轨道
*   吉拉
*   松弛的
*   Windows/Mac/*nix 机器

实验室中使用了大量上面没有提到的工具。关于团队动力的一个很好的事情是，如果你能就 X 工具为什么有用提出一个论点，那么你就能使用它。

## 团队动态

[![Intern Group](img/fdae92709f71b202a9768f18f77f40ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4LE9Qop8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/JyWQI1k.jpg)

当你加入 JPL 时，你加入了一个特定职能的团队。例如，有一个数据服务团队、用户界面团队和人工智能团队。然后这些小组的成员被招募去执行任务。作为一名个人贡献者，你可以把 100%的时间花在一项任务上，也可以把时间分配在多项任务上。

切换团队/任务非常简单。你会遇到其他团队的主管或任务的项目负责人。一旦你找到了一个可以接受的团队/任务，你就可以为了一个平稳的交接而履行当前的义务。

根据你所在的团队，你可以坐在单独的办公室、小隔间或开放空间。

## 俱乐部

[![Interns during the Aerospace Games](img/90210897a785cf8c6d27f86a1d6447e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c99Wc44t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/FNY6B4G.jpg)

实验室有各种兴趣的俱乐部。我最喜欢的是健康俱乐部、自行车俱乐部和园艺俱乐部。

## 面试流程

JPL 没有统一的面试程序。如果一个团队真的喜欢你，你可以当场被录用。以下是建议的流程:

1.  在线申请特定的职位或获得推荐
2.  电话面试人力资源(行为)
3.  与团队/任务负责人进行一轮电话/视频访谈(行为/技术)
4.  现场面试(行为/文化/技术)
5.  决定通过电子邮件/电话发送

## 生命中的一天

|  |  |
| --- | --- |
| 上午 8 点 | 醒来 |
| 上午 8 点 20 分 | 早餐 |
| 上午 8 点 45 分 | 去上班 |
| 上午 9 点 | 到达工作地点 |
| 上午 9 点 15 分 | 补上电子邮件/空闲时间 |
| 上午 9:30 | 竖立 |
| 上午 10 点 | 程序调试时间 |
| 中午 12 点 | 午餐/实验室或嘉宾演讲 |
| 下午 1 点 | 程序调试时间 |
| 下午 2 点 | 会议/冲刺计划/回顾 |
| 下午三点半 | 程序调试时间 |
| 下午 5 点 | 加州理工学院的健身房 |
| 下午 6 点 | 主页 |

## 引人注目的额外津贴

*   你可以选择一个正常的休息日工作计划，每隔一个星期五休息一天。
*   补贴午餐
*   远程工作友好型
*   访问加州理工学院的资源，如健身房、讲座和设备租赁
*   客人或其他员工就项目或其他主题进行的每周讲座

## 关闭

在这里工作非常愉快(没有双关语的意思)。人们都很聪明，认识新朋友是一种放松的方式(实验室鼓励你通过每两周安排一次外出喝咖啡来认识新朋友)。与在大科技工作相比，这是一个明显的变化，但你在这里产生的影响在太空中随处可见。

* * *

如果你发现自己在南加州，并想看看实验室，随时打电话给我！对于实验室来说，这是一个激动人心的时刻，因为火星 2020 漫游者正在这里建造。