# Xamarin WatchOS，为 Apple Watch 打造一款游戏

> 原文：<https://dev.to/andreslopezrm/xamarin-watchos-create-a-game-for-apple-watch-322c>

Xamarin 是一个开发平台，它不仅仅是为 iOS 和 Android 开发跨平台的应用程序。

在本教程中，我们将看到如何在 Xamarin 中为 WatchOS 创建一个应用程序。

# 要求

*   Mac 版 Visual Studio
*   Xcode 10
*   Mac OS Mojave
*   iPhone 模拟器
*   苹果手表模拟器

# 创建解决方案

在本教程中，您将基于石头、剪刀和布的游戏为 Apple Watch 创建一个小游戏，并展示 Xamarin 在创建解决方案时提供的一些巨大可能性。

首先，我们在 Visual Studio for Mac 中创建了一个新的解决方案。

[![alt xamarin](img/1ed925e32d846707b9bb6e6fc194113d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6WmyfXKV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A6RIgTTsHJMJRxB-NXa025w.png)

一旦创建了解决方案，我们将添加 WatchOS 项目。

[![alt xamarin](img/68b5754c242cb395e63390df34c64c43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sWubyGH8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ADXRMGbaim4bGoQ22en9ahw.png)

我们选择 Watchkit 应用程序。

[![alt xamarin](img/77c2bd8ca7eb27c575b840c18b4e9e72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nu1QsQJ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A9LjzFN6D34FqwFT3xZOcFg.png)

取消选中包括通知选项。

[![alt xamarin](img/9c9f478c8425464f4e24c29f67d66c02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KIR5D59A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AwxXJUOI5afoJFFsbqpR4lQ.png)

# 设计

图形界面应该如下所示:

[![alt xamarin](img/3170dddbcf550fe671a63d6fb661b89b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--691N1M4---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ag_9qo7DuaCisuvRDumHK8g.png)

界面的结构与 iOS 拖动控制中的结构相同，应该如下所示:

*   **贴上标签**看谁选择了手表(石头、布或剪刀)
*   **标注**查看结果(赢、输、平)
*   **白色**细线分隔符
*   **带三个按钮的群组视图**
*   **群组视图**左边是标签，右边是计时器

查看结果的标签必须具有与时钟大小成比例的高度，以便在 size 部分中分配这些值。

*   宽度，相对于容器赋值为**，值为 1，取 100%的宽度。**
*   相对于容器的高度**被赋值为 0.4，这将占时钟高度的 40%。**

[![alt xamarin](img/adb5a3558ea236a75b63a3d8a2bfb4db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oU7wbqF3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A4u5-Ly26ik5vf7d5e9gBVw.png)

一个**组视图**是水平并排容纳每个控件的视图。

[![alt xamarin](img/737dbe8518a11908cdc53a6f036bc4b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i32IXhzK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AWhirGmrNzVAjoTdq3SkMjQ.png)

将 GroupView 中的控件在工具箱的 align 部分中与 right value 右对齐。

定时器是一个控件，以标签的形式显示一种时钟。

[![alt xamarin](img/4b56649e56519d6dc0df430b3f813cf2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Teqp1lFa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A2jW3PWRDk32PTK4SHZPqqg.png)

# 代码

*   声明一个包含 3 个选项和一个计数器的安排，以显示赢得的游戏数。

*   初始化时，我们将图像放在按钮上，计时器开始显示经过的时间。

*   用如何确定获胜者的逻辑创建一个函数。
    控制器的完整代码如下。

```
 using System;

using WatchKit;
using UIKit;
using Foundation;

namespace RockPaperScissorsXamarin.RockPaperScissorsWatchExtension
{

    public partial class InterfaceController : WKInterfaceController
    {

        private int Counter = 0;

        private enum Results { TIE, WIN, LOSE };
        private enum Moves { Rock, Paper, Scissors }

        private Moves[] Options = { Moves.Rock, Moves.Paper, Moves.Scissors };

        protected InterfaceController(IntPtr handle) : base(handle)
        {

        }

        public override void Awake(NSObject context)
        {
            base.Awake(context);

            RockButton.SetBackgroundImage(new UIImage("rock"));
            PaperButton.SetBackgroundImage(new UIImage("paper"));
            ScissorsButton.SetBackgroundImage(new UIImage("scissors"));

            Timer.Start();
        }

        public override void WillActivate()
        {
            Console.WriteLine("{0} will activate", this);
        }

        public override void DidDeactivate()
        {
            Console.WriteLine("{0} did deactivate", this);
        }

        partial void RockButton_Activated()
        {
            Check(Moves.Rock);
        }

        partial void PaperButton_Activated()
        {
            Check(Moves.Paper);
        }

        partial void ScissorsButton_Activated()
        {
            Check(Moves.Scissors);
        }

        private void Check(Moves selection) 
        {
            var random = new Random();

            var position = random.Next(0, Options.Length);
            var machineChoose = Options[position];

            if (selection == machineChoose)
            {
                ResultLabel.SetText(Results.TIE.ToString());
            }
            else if (selection == Moves.Rock && machineChoose == Moves.Scissors)
            {
                ResultLabel.SetText(Results.WIN.ToString());
                Counter += 1;
            }
            else if (selection == Moves.Rock && machineChoose == Moves.Paper)
            {
                ResultLabel.SetText(Results.LOSE.ToString());
            }
            else if (selection == Moves.Paper && machineChoose == Moves.Rock)
            {
                ResultLabel.SetText(Results.WIN.ToString());
                Counter += 1;
            }
            else if (selection == Moves.Paper && machineChoose == Moves.Scissors)
            {
                ResultLabel.SetText(Results.LOSE.ToString());
          }
            else if (selection == Moves.Scissors && machineChoose == Moves.Paper)
            {
                ResultLabel.SetText(Results.WIN.ToString());
                Counter += 1;
          }
            else if (selection == Moves.Scissors && machineChoose == Moves.Rock)
            {
                ResultLabel.SetText(Results.LOSE.ToString());
          }

            WinnerLabel.SetText($"Wins: {Counter}");
            OponentLabel.SetText($"Oponent: { machineChoose }");

        }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

项目执行和应用程序执行如下:

[![alt xamarin](img/3fe7fbede31c11cb5a0d12f6e2f6a414.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kCC2lXuq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AX4O6h4kfT_Z5dpmcKklLmA.png)

# 结论

在这个例子中，我们看到了 Xamarin 在创建应用程序时提供的广泛可能性，增加了对 WatchOS、TVOS、Android Wear 甚至 Android TV 应用程序的支持。😄

你可以在 [Github](https://github.com/andreslopezrm/XamarinWatchOSRockPaperScissors) 上查看应用代码