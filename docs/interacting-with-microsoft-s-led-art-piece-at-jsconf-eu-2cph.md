# 在 JSConf EU 上与微软的 LED 艺术作品互动

> 原文：<https://dev.to/azure/interacting-with-microsoft-s-led-art-piece-at-jsconf-eu-2cph>

嗨 CSSConf/JSConf 欧盟朋友！如果你在这里，那么你可能听说过我们带了一件 LED 艺术品去参加会议。这篇文章将告诉你关于这个项目的所有信息，以及你如何参与其中。

这件艺术品的灵感来源于竹林。该设备具有多个 LED“拍摄”，由您从云中控制。硬件是由我出色的队友、微软欧洲云倡导者 Jan Schenk 根据我的设计制作的。这是艺术作品的最终版本:

[https://www.youtube.com/embed/Y4pN53zziyA](https://www.youtube.com/embed/Y4pN53zziyA)

乍一看，为这个作品创建动画可能会令人望而生畏，但不要担心。这些年来，我在这个项目上投入了大量的工作，以使它能为各种各样的人所接受。

这个项目是基于我创建的另一个名为 [Raver Lights](https://github.com/nebrius/raver-lights/) 的项目。我早在 2016 年就开始了这个项目，以控制用于燃烧的人和类似活动的艺术品上的 led。可以说，它在这一点上已经经受了很好的考验，已经多次在沙漠的极端环境中幸存下来。

有两种方法可以控制这个作品中的动画。第一种方式是在我们的展台上，使用运行在新的基于铬的 Edge 版本上的网络界面。).第二种是通过创建你自己的 [Azure 基于功能的无服务器端点](https://docs.microsoft.com/en-us/azure/azure-functions/?WT.mc_id=devto-blog-brhugh)，这给了你比展台界面更多的对动画的控制。

## 基本动画

别让名字骗了你，这些动画没什么无聊的！老实说，我只是不擅长给事物命名。

在展台上，你可以从我们手头的平板电脑上提交一个动画，这些平板电脑运行新的基于铬的 Edge 版本。如果你还没听说，我们[正在重写我们的浏览器，使其基于 Chromium](https://github.com/MicrosoftEdge/MSEdge) ！我们仍在努力让它为普遍使用做好准备，但你今天就可以为 Windows 10 *和* macOS 下载 canary 和 dev channel 版本。

我决定在构建这个应用程序时找点乐子。我的设计技能可能不是最好的，但我确实在这里使用了一些非常新的技术:CSS Grid、新的 CSS filter 属性和 Web Assembly。如果我在展台，就全问我是怎么写的(紫色头发的是我)。你也可以在 GitHub 上查看[代码。](https://github.com/nebrius/azure-led-booth/tree/master/clients)

虽然这些动画确实很漂亮，但是你不能像自定义动画一样得到同样的控制。

## 自定义

你也可以编写一个自定义的 Azure 函数来获得对动画的完全控制。出于好奇，下面是创建您在展台上看到的默认动画的代码:

```
import {
  createWaveParameters,
  createMovingWave,
  createSolidColorWave,
  createPulsingWave
} from 'rvl-node-animations';

const animation = createWaveParameters(
  // Create the moving purple wave on top
  createMovingWave(215, 255, 8, 2),

  // Creating a pulsing green on top of the blue, but below the purple
  createPulsingWave(85, 255, 2),

  // Create the solid blue on bottom
  createSolidColorWave(170, 255, 255, 255)
); 
```

这段代码使用了与创建自定义动画相同的工具。还不错，对吧？相信我，编写采用这些参数的动画引擎要困难得多。

要开始编写自定义动画，请访问我们的 [starter repo](https://github.com/bnb/jsconf-eu-led) 并阅读那里的说明。

黑客快乐！