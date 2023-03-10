# 一种实现定向碰撞检测的方案

> 原文：<https://dev.to/webdva/a-proposal-for-achieving-oriented-collision-detection-5487>

我有一个简单的解决方案，可以解决三维空间中发生的碰撞检测的方向问题。

这是在考虑一个伪大型多人在线游戏的想法，像战斗一样有黑暗的灵魂。

由于黑暗灵魂战斗的本质包括近战武器的摆动，假设武器的方向或旋转需要被考虑以提供精确的碰撞检测。

要考虑的一个问题是艺术风格，这将影响游戏的许多方面。

受非常聪明和热情的游戏开发者 Dek 的启发，他的游戏 [Hordes.io](https://hordes.io) 为角色使用了创新和简单的艺术风格。

[![Hordes.io art style](img/4956e6bb151c92b40dd4924f7de67f7b.png "Hordes.io art style")](https://res.cloudinary.com/practicaldev/image/fetch/s--E4lIAqQw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/05kc3utj5zbz65adcowq.gif)

对于像战斗这样的有黑暗灵魂的假想游戏，每个武器将完全由一个简单的矩形盒子组成。如果使用提议的简单艺术风格，由于碰撞盒与假设的游戏角色和近战武器具有完全相同的形状和大小，这将有助于非常精确的碰撞检测。

# 技术实施方案

我提出了一种可能的解决方案来实现三维空间中两个碰撞盒之间的碰撞检测，并考虑了方向:使用仿射变换来将一个碰撞盒顶点的坐标框架转换到另一个碰撞盒的坐标框架。

使用变换操作符 *T1* ，表示要变换的碰撞框的原始坐标框架，以及 *T2* ，表示它将被变换到的第二个碰撞框的坐标框架，在列向量 *x1* 上，即第一个碰撞框的未变换顶点，将看起来像

[![equation](img/388ed64898ab2966a092bd77afdc215e.png "equation")](https://res.cloudinary.com/practicaldev/image/fetch/s---2CP4Gbl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hydjz3gn6c7e0ijkxbu0.png)

无论真正的 4×4 矩阵乘法的顺序和方向是什么。新的矢量点 *x2* 将有助于确定两个定向碰撞盒之间是否发生穿透。

这应该是一个简单的解决方案，它看起来几乎天真，就像是非常可取的。

我不知道这样的方法或解决方案是否会达到其预期的效果，因此我需要进行实际的实验，看看它是否可行，以及简单地使用仿射变换来实现考虑了方向的碰撞检测的假设是否成立。

# 实际考虑

然而，现实世界的限制目前阻止了这种实验行为的容易执行，例如没有测试空间或环境的特权，使用笔和纸是我现在能做的最好的事情，并且受到所有决策中必须考虑的繁荣计划的驾驶时间限制的限制。

然而，注意，驾驶时间约束可以允许利用所得到的灵活的性能约束，例如通过允许决策者(me)认为不准确的碰撞检测是可接受的，这可能导致例如根本不考虑碰撞检测系统中的方位。