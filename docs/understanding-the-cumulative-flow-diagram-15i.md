# 理解累积流程图

> 原文：<https://dev.to/svikashk/understanding-the-cumulative-flow-diagram-15i>

[![Understanding the Cumulative Flow Diagram](img/ccce8515016695cf78247d2b6d182883.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6DjNpM8n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.zepel.io/conteimg/2019/01/blog_bg.png)

累积流程图是最常用的图表之一，用于跟踪敏捷团队的进展。您可以只使用一块看板来生成图表，将任务跨列移动。不需要额外的数据。

## 什么是累积流量图(CFD)？

CFD 是简单的堆积面积图，显示看板每列中的任务数量。最下面一行表示在任何时间点处于完成状态的项目数。这条线的进程也作为整个过程的燃耗图。

读取累积流程图:

考虑一个简单的 CFD，看板有 4 列:Backlog、In Development、QA 和 Deployed。

[![Understanding the Cumulative Flow Diagram](img/53bce92511d53efbd7722f443f4f05b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uTKVNseM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.zepel.io/conteimg/2019/01/graph-1-1.png)

<center><small>The Cumulative Flow Diagram</small></center>

该图表显示了一段时间内工作流每个阶段中的项目数量。要了解任何时间点的工作分配，只需阅读特定日期的图表。

图表的最底部(紫色区域)显示了纸板中已完成项目的数量。随着团队完成越来越多的项目，图表的这一部分预计会继续增长。该部分也用作燃耗图。

这上面的线表示板上的其他状态和每个状态中的任务数量。最上面一行表示积压。

[![Understanding the Cumulative Flow Diagram](img/26fa618a6557fa82c32f0ca2cdc10763.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A_zyQE2B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.zepel.io/conteimg/2019/01/graph-2-2.png)

<center><small>Scope changes may be consistent or in chunks</small></center>

**进行中的工作**由非“backlog”或“deployed”状态的线之间的区域表示。这是一个重要的衡量标准，因为它是团队绩效和方向的最大指标。

当新的项目被添加到 backlog 中时，范围变化发生。许多团队不断地指定用户故事，并将它们添加到开发 backlog 中。当大的史诗被指定并且任务被加在一起时，在陡峭的台阶上看到突然的范围变化也是很常见的。

常见的累积流程图模式

**理想累积流量图:**

[![Understanding the Cumulative Flow Diagram](img/06e3ef7cd3b1ba7b435e794cbf2c466f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hrrhY_Ex--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.zepel.io/conteimg/2019/01/graph-3-2.png)

<center><small>An ideal CFD grows steadily while maintaing the WIP</small></center>

**一个理想的 CFD 是这样的:**

<u>竣工项目稳步增长</u>。这表明团队能够稳定地完成任务并接受新的任务。

所有进行中的线或多或少是平行的。这表明不同阶段中的工作一直在完成并转移到工作流中的下一个阶段。

积压的工作不断增加。这表明特性是以与开发团队的速度相匹配的速度被挑选和指定的。然而这种情况很少发生。项目往往被成批地添加到待办事项列表中，尤其是在会议之后。

当一个团队在他们工作流程的任何部分挣扎时，图表就会偏离它的理想形式。发生这种情况时，您应该找出阻碍因素，并在[站立会议](https://kanbanize.com/blog/running-a-better-stand-up-meeting/)上解决它们。

**正在进行的工作不断增加:**

[![Understanding the Cumulative Flow Diagram](img/554f4cd7f792e2bce57d34e36f072bf1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w48bft8c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.zepel.io/conteimg/2019/01/graph-4-1.png)

<center><small>The team is taking up more work without completing current tasks</small></center>

该图显示团队在没有完成当前任务的情况下承担了更多的工作。这是一个令人惊讶的常见模式，尤其是对于没有组织的团队。像这样的图表通常是一个指标，表明最后期限将无法满足，目标将无法实现。

需要快速识别这种趋势，并采取纠正措施。防止这种情况发生的最好方法是为看板中的列设置 [WIP 限制](https://www.excella.com/insights/how-to-set-initial-wip-limits)。

**一个团队受阻**

[![Understanding the Cumulative Flow Diagram](img/cfe55455e797472c5215a8c432715c57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ayaw-thE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.zepel.io/conteimg/2019/01/graph-5-2.png)

<center><small>The development team is not completing tasks fast enough to keep up</small></center>

当一个团队被阻塞时，该团队的累积流量图下的区域不断减少。受阻的团队无法跟上其他完成任务更快的团队。

团队可能被阻塞的一个很好的指标是特定状态的平坦线。例如，在上面的例子中，“开发中”开始停滞，因为他们不能完成任务并进入下一个状态。使用站立会议来解决阻碍因素并防止这种情况发生是至关重要的。设置和维护 WIP 限制也有助于防止这种情况。

**多支队伍同时受阻**

[![Understanding the Cumulative Flow Diagram](img/9d44ef7b8686fc118e02019b5db7d509.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E6CaxQyM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.zepel.io/conteimg/2019/01/graph-6-2.png)

<center><small>The progress of both development and QA teams have flatlined</small></center>

当多个团队被阻塞时，他们各自的状态一直是平坦的——导致了如上图所示的图表。当整个团队已经停止进展去做不同的事情时，就会出现这种情况。最常见的情况是当整个团队停止开发新功能来修复一个主要的产品 bug 时。

有时，你的图表可能在一个长周末或团队假期结束时看起来像这样——所以一定要理解 CFD 持平的根本原因。

## 使用有效累积流量图:

CFD 是保持看板流程正常运行的好方法。它们易于理解，可以从很少的数据中生成可视化效果。

像产品负责人的所有其他工具一样，遵循一些最佳实践可以帮助平滑流程并提高团队的效率。

*   一个理想的 CFD 不断向上发展，有稳定数量的**在制品(WIP)项目。**
*   为看板设置正确的列数会使图表更具可读性。
*   不断增加的 WIP 会导致更多的会议和错过最后期限。
*   WIP 应该增加的唯一时间是新成员加入团队的时候。
*   强制实施 WIP 限制，在阻碍因素开始影响其他团队之前发现并消除它们。
*   使用累积流程图来帮助跟踪你每天站立时的进度。

[![Understanding the Cumulative Flow Diagram](img/9d00149b6edfc4ca6e85db8fd76b9aa4.png)](https://zepel.io/?utm_source=devto&utm_campaign=cfdblog)