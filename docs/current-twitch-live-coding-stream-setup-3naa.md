# 当前 Twitch 实时编码流设置

> 原文：<https://dev.to/michaeljolley/current-twitch-live-coding-stream-setup-3naa>

我对流式编码会话仍然非常陌生，但我已经迷上了观看这样的流:

*   [弗里茨&的朋友](https://www.twitch.tv/csharpfritz)
*   [Clarkio](https://www.twitch.tv/clarkio)
*   [罗伯特表](https://www.twitch.tv/roberttables)
*   [Talk2meGooseman](https://www.twitch.tv/talk2megooseman)
*   [DevChatter](https://www.twitch.tv/devchatter)
*   [节点](https://www.twitch.tv/noopkat)

有如此多的优秀流使用各种技术进行实时编码。因此，我开始更加努力地尝试让我的设置正确，这样我就可以编写一些个人项目的代码，并希望帮助其他人学习新技术。

这真的很有帮助，因为我开始看到这些 streamers 及其硬件和软件设置生成的博客帖子和内容，所以我有动力提供类似的东西，希望其他人可以启动一个伟大的流并推进社区。

`Full disclosure: the links below are affiliate links with Amazon. Buying from them helps support my stream.`

所以不要再拖延了:

## 音频/视频设置

我还在学习很多关于灯光和音响的知识。现在在这些领域有很多实验正在进行，所以下面的很多内容可能会改变。

在我的显示器上方，我有两个 [Elgato 钥匙灯](https://amzn.to/2Te5LOd)面板。两者都设置为提供更多的黄光而不是蓝光，以抵消来自我的 3 24 英寸显示器的蓝光。

[![](img/913fcbec47dc19e2a7b94dcb28c611e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tbDAYTYI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1228996/53291179-d9b89f00-3774-11e9-8aca-ade57af31e10.png)

在我身后是一个由 [Fovitec 夹子支架](https://amzn.to/2U52qy4)支撑的[fovi tec studio pro](https://amzn.to/2BPi7m2)5’x 6.5’蓝/绿色屏幕。

我的灯光设置并不完美，我仍然在调整水平来提高。我觉得我身后需要另一种类型的光来更好地照亮绿色屏幕，以改善色度键。实际上，我一直在考虑将其中一个关键灯移到前面中间，将另一个移到我身后来照亮绿色屏幕。

我一直在用[罗技 C922x 网络摄像头](https://amzn.to/2ErynLP)拍摄我的主要视频。它似乎做得很好，我目前正在使用 Xsplit Vcam 软件来处理背景移除。我根据其他流媒体的反馈购买了网络摄像头，并没有对它感到失望。

对于音频，我使用的是带 pop 滤波器的电容麦克风。它进入一个[的 USB 音频接口，然后从那里进入电脑。我对麦克风非常满意。我们公司大量使用 MS 团队进行通话/视频录制，我现在只使用这款话筒，而不是耳机话筒。](https://amzn.to/2U3ACKv)

## 其他硬件

我的流一直专注于物联网开发，我发现很难在流上显示我的 [Raspberry Pi](https://amzn.to/2EhMOkr) 视频发生了什么。到目前为止，我仅限于向 Pi 显示一个 SSH 窗口。所以这个家族的新成员是 [Elgato HD60 Pro](https://amzn.to/2XhXwQl) 游戏捕捉卡。这接收一个 HDMI 输入，并在 OBS 中显示为一个视频源，我可以在流上共享。这将在几天后的溪流中首次使用。

[![](img/0ab2a15bf6c13df89d8bf7a7caf8d3bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dV44bHoy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1228996/53291452-36b65400-3779-11e9-946d-5afe7562919c.png)

说到在物联网设备上工作，我经常遇到的问题是将键盘和鼠标连接到它们，然后当我切换设备或放好设备时，必须重新布线。然后 [Scott Hanselman](https://www.hanselman.com/) 让我打开了[ipazsport 无线迷你手持键盘/触摸板](https://amzn.to/2Npx0jt)。

这是一个带有内置触摸板的迷你键盘，如果你使用像 Rapsberry Pi 这样的设备，这是*必须拥有的*。它只卖 14 美元，所以买它是很容易的。

我一直在寻找使用的最后一个硬件(即使在没有流媒体的时候)是 [Elgato Stream Deck](https://amzn.to/2Er27bE) 。这个小设备是一个奇迹，随着 Elgato 发布的新 SDK，我有一种感觉，一个扩展/附加的生态系统即将围绕这个家伙涌现。

## 软件

我没有使用太多的软件，但这里有一个纲要:

*   [OBS](https://obsproject.com/) 用于场景和实际录制/流式传输。这里也做一些音频过滤，虽然这仍然是一个正在进行中的工作。
*   [Elgato Game Capture for Windows](https://www.elgato.com/en/gaming/downloads)捕捉来自 HD60 Pro 的输入。
*   在我的流上，你会看到由 [StreamLabs](https://streamlabs.com/) 提供的提醒和小工具。它们使得向您的流添加定制的提醒变得容易。

* * *

那是目前的破败景象。我希望在努力改进流的音频/视频制作时，能够详细阐述我的发现，以便未来的流可以更快地到达那里。总之，我真的很喜欢在流上进行现场编码。最重要的是，我真的从建立的关系中得到了很多，我经常对所有参与者的友好和乐于助人感到惊讶。

对改进我的流或其他硬件/音频/视频想法有任何建议吗？给我留言或者在 Twitter 上找到我。