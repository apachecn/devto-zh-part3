# 是时候 Apple Watch 了，用 WatchOS [Swift] ⌚️创造一个游戏

> 原文：<https://dev.to/andreslopezrm/its-time-to-apple-watchcreate-a-game-with-watchos-swift--2660>

在本教程中，您将创建一个小游戏👾基于石头、布、剪刀游戏的苹果手表👊✂ 📃。

# 要求

*   Mac OS Mojave 或更高版本。
*   Xcode 10 或更高版本。
*   iPhone 模拟器。
*   Apple Watch 模拟器。

首先，我们在 Xcode 中创建了一个新的项目，类型为 **WatchOS** 。

[![alt text](img/85cff20da77211562a9e336d59d12a0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pnz8YOIp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ASxE6RlXihptafGFCkJBvsg.png)

用值“RockPaperScissorsSwift”填充产品名称输入，然后取消选中选项**包括通知场景**。

[![alt text](img/be119cba0afffb13b7445e73e728830d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NET9VD_M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ABeKaYkZumg_4Q0AeKV3uNw.png)

🖥一个普通的 iOS 解决方案是通过两个附加项目创建的。

*   **WatchKit App** (故事板去向)
*   **手表套件扩展**(控制器代码)

[![alt text](img/852ae95330272332160b0b2821a56b84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nGKyfS4H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ApSxW4fvZQeBl8QlZkyICxg.png)

项目的图形界面应如下所示:

[![alt text](img/0c436b8277fd123b491a9a5b184359ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iCIwzpwu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AncWez831PVhfc-BGpx9mzA.png)

🖐❇️界面的构造与 iOS 中的拖动控件相同，应该如下所示:

*   **贴上标签**看谁选择了手表(石头、布或剪刀)
*   **标注**查看结果(赢、输、平)
*   **白色**细线分隔符
*   **带三个按钮的群组视图**
*   **群组视图**左边是标签，右边是计时器

查看结果的标签必须具有与时钟大小成比例的高度，以便在**大小**部分分配这些值。

*   **宽度**，相对于容器赋值为 1，取宽度的 100%
*   **相对于容器的高度**被赋值为 0.4，这将占时钟高度的 40%。

[![alt text](img/1ae31bb4bd462f743e527c11a9d9fb20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lZxoFHiq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A7ay78OwcCX4PtApDfdPNqQ.png)

一个**组视图**是水平并排容纳每个控件的视图。

[![alt text](img/2b2c18f37844253afe916b9aa3bba541.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BS7EK2OQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A1jKEs28DoZBKWea47qn3Tw.png)

将 GroupView 中的控件在 align 部分的工具箱中靠右对齐。

[![alt text](img/28b6a26e770fdf6bba67bc180b434ac7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ivb4VahL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Az3UizrF5_ITbeUNppXrA7w.png)

**定时器**是以标签形式显示时钟类型的控件。

[![alt text](img/03b3512cb988b3674b0b19d52c7557a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a--1h7Je--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AO-quZgQ5bWW6Wi0CyQcaTg.png)

# 代号🛠

*   声明一个包含 3 个元素和一个变量计数器的数组选项，以显示赢得的游戏数。
*   初始化时，我们将图像放在按钮上，计时器开始显示经过的时间。
*   用如何确定获胜者的逻辑创建一个函数。

控制器的完整代码如下: