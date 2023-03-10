# pCloudy 5.4 的新功能

> 原文：<https://dev.to/pcloudy/what-s-new-in-pcloudy-5-4-27ek>

我们很高兴地宣布 pCloudy 5.4 的发布，它具有令人兴奋的新功能，使应用测试比以往任何时候都更简单。你可能对 pCloudy 很熟悉，但对于新手来说，pCloudy 是一个基于云的移动应用测试平台。我们在短时间内实现了许多里程碑，因为我们立即解决问题并定期引入新功能。典型的例子:这个新版本的 pCloudy，我们已经解决了用户关心的问题，并添加了新的特性来优化测试。让我们来看看 pCloudy 5.4 中添加的所有功能。

**CTRL + V**
要在 pCloudy 设备上测试你的应用，你需要登录 device.pcloudy.com，然后点击设备选项卡预订设备。只需点击所选设备旁边的可用按钮，即可进入设备交互页面。在之前的设备窗口中，您必须使用虚拟扩展键盘在设备中输入文本。尽管现在你可以从你的系统中复制任何文本并直接粘贴到设备中。因此，现在不使用扩展键盘，只使用两个按钮在设备中输入字符串。当使用多种设备需要多次输入长文本时，这将节省时间和精力。

**FollowMe 新用户界面**
要使用 FollowMe 功能，您必须对您想要在其上执行测试的应用程序进行测试。在“devices”部分，单击“FollowMe”选项卡，然后在下一个屏幕中选择应用程序，选择您将执行测试的持续时间和设备。在下一个屏幕中，您将在窗口的右上角看到您选择的设备和两个选项(软键和屏幕截图)。现在，当您在主设备上执行任何操作时，该操作也会在辅助设备上复制。如果你输入文本，点击按钮，滑动等。在主设备中，这将在辅助设备中同时发生。

[![](img/4d4d71106e99a831ebe5ebdca48b1bc5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q7_nfiur--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/05/pcloudy-5.4-new-UI.png)

pCloudy 5.4 新 UI
欲了解更多信息，请参考此链接:-

[https://www . pcloudy . com/mobile-application-testing-documentation/manual-app-testing/follow-me-app-testing . html](https://www.pcloudy.com/mobile-application-testing-documentation/manual-app-testing/follow-me-app-testing.html)

**预装 app 上的 Appium**
在 pCloudy 中，你可以在多个设备上并行运行 app ium 测试。要使用 Appium 执行测试，您需要输入功能。一些用户希望多次测试他们的应用程序，以便在同一个应用程序上执行不同类型的测试。要做到这一点，用户必须一次又一次地安装应用程序。但现在我们发布了一个更新，通过它你可以在预装的应用上使用 Appium 进行不同类型的测试。这将节省您的时间，并使过程更容易。

[![](img/8293182af8feaf4c8a1002565b6ee694.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hQOdMCoi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/05/Appium-on-Pre-Installed-Apps.png)

**预装应用程序上的应用程序**
因此，如果您已经在设备中安装了应用程序，并且您想要在同一应用程序上执行测试，那么您不需要通过应用程序名称功能。

有关更多信息，请参考此链接:-

[https://www . pcloudy . com/mobile-application-testing-documentation/automation-testing/inspect-element-using-app ium-desktop . html # running-app ium-scripts](https://www.pcloudy.com/mobile-application-testing-documentation/automation-testing/inspect-element-using-appium-desktop.html#running-appium-scripts)

**GitHub 集成**
pCloudy 集成了最流行的工具和框架，方便测试。很多用户也要求集成 Github 库，所以我们做了。现在你可以直接上传你的任务，从 pCloudy 提出 GitHub 库中的缺陷。这个特性的添加也是为了方便我们的用户通过 pCloudy 测试和管理任务。

[![](img/a020b4cc5d9a55acbdcaccb853abb4a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mGBdVwAf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/05/GitHub-integration.png)

**GitHub 集成**

要使用 GitHub，只需登录 device.pcloudy.com，点击窗口右上角的用户名。在下拉列表中点击设置，在设置窗口中点击 GitHub 选项卡，输入 URL 和访问密钥进行注册。现在在设备会话界面中，你可以点击协作，然后选择在 GitHub 中记录一个 bug。将打开一个新窗口，您可以在其中看到存储库列表，输入问题类型，输入标题，添加描述，然后单击“单击在 GitHub 中记录错误”按钮。然后你可以随时去 GitHub 查看所有提出的问题。

> 我们正在开发更多的新功能，很快我们就会推出新版本的 pCloudy。这是一个持续的过程，我们总是试图解决用户的问题，并定期向他们提供更新。这就是为什么我们正在达到新的高度，我们将继续努力提供最好的服务。