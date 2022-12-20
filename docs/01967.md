# 务实的程序员——第 8 章注释和问题

> 原文：<https://dev.to/cseeman/engineering-bookclub-pragmatic-programmer-chapter-8-notes-and-questions-320j>

很抱歉花了一点时间来张贴，但让我们深入到务实的程序员的最后一章！一如既往地感谢您

[![amcaplan image](img/4da757e95b9724fa6190a02d2e73caee.png)](/amcaplan)

## [阿里尔·卡普兰](/amcaplan) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;User&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:3288,&quot;name&quot;:&quot;Ariel Caplan&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow user: Ariel Caplan" aria-pressed="false">跟随</button>

of [Dev Empathy Bookclub](https://devempathybook.club/) for letting me use questions from the book club. If you are looking for an awesome group of people to discuss books that can help you become a better person and better developer check them out! Right now they are taking on [Say What You Mean: A Mindful Approach to Nonviolent Communication](https://www.amazon.com/Say-What-You-Mean-Communication/dp/161180583X) by Oren Jay Sofer.

关于第 8 章“务实项目”的注释

*   代码质量是团队的问题
*   团队不能容忍窗户被打破，团队必须有责任感
*   项目团队内部的沟通很重要，项目团队与公司其他部门的沟通也很重要
*   不要在团队成员之间重复工作
*   根据功能将团队划分为自组织的小组
*   尽可能实现自动化，不要依赖手动流程(例如安排备份、编译项目、生成代码、回归测试、构建…)
*   “寻找虫子有点像用网捕鱼。我们用细而小的网(单元测试)来捕捉小鱼，用粗而大的网(集成测试)来捕捉鲨鱼。”
*   越早发现漏洞，修复它的成本就越低
*   了解最终用户的使用模式，以及它们与测试数据/模式的不同之处
*   如果一个 bug 从现有测试的网中逃脱，您需要添加一个新的测试来在下一次捕获它。
*   所有文档都是代码的镜像，如果有不一致的地方，代码才是最重要的
*   代码注释应该讨论做某事的原因、目的和目标(代码是如何做的)
*   任何形式的文档都只是代码的快照
*   传达期望，与用户合作，让他们了解你将交付什么
*   对自己完成的代码感到自豪，不要嫉妒地维护它，但要证明它是可靠的、写得好的、经过测试和记录的代码。
    *   在受到责备之前！所以现在你的名字在他们的无论什么现在！😜
    *   有强烈的观点，但不太坚定

技巧

*   围绕功能而不是工作职能进行组织
*   不要使用手动程序
*   早点测试。经常测试。自动测试。
*   在所有测试运行之前，编码还没有完成
*   使用破坏者来测试你的测试
*   测试状态覆盖率，而不是代码覆盖率
*   findbugs once
*   将英语视为另一种编程语言
*   构建文档，不要把它附加上去
*   温和地超越用户的期望
*   在你的作品上签名

就第八章提出的问题

*   还需要单独的工具构建器工具吗？还是每个程序员也要构建自己的工具？
*   “我们的目标是维护一个自动的、无人值守的、内容驱动的工作流”这是我们作为开发人员在拥有 20 多年的工具和实用程序后能够实现的吗？
*   您的项目/应用程序中缺少哪些测试？为了前任。想想可能存在多少单元/集成测试，但是验证和确认呢

测试？这本书经历了几个主要类型:

*   1.单元测试
*   集成测试
*   验证和核实
*   资源耗尽、错误和恢复(压力测试)
*   性能试验
*   可用性测试

*   书中的提示 60 敦促:围绕功能而不是工作职能来组织

    *   你以前在跨职能团队工作过吗？这种体验与基于功能(仅后端、仅前端、仅数据库等)的工作相比如何？)团队？
*   在提倡可用性测试的同时(第 43 章，无情的测试)，这本书声称:不满足可用性标准就像被零除一样是一个大错误。这是你工作过的团队的态度吗？你相信这是真的吗？

进一步阅读

[https://en.wikipedia.org/wiki/Chief_programmer_team](https://en.wikipedia.org/wiki/Chief_programmer_team)

在 1999 年 4 月《CACM》的一篇文章中，罗伯特·格拉斯总结了一些研究，这些研究似乎表明，虽然代码检查是有效的，但是在会议中进行评审却不是[Gla99a]。

就这样，我们结束了！！我的工程读书会的下一个主题是我们是如何走到今天的:六项创新成就了现代世界，对此有什么想法/感受？你阅读清单上的下一篇是什么？