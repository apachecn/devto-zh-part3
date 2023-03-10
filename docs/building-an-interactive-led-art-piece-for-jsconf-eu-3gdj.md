# 为 JSConf EU 制作互动 LED 艺术作品

> 原文：<https://dev.to/azure/building-an-interactive-led-art-piece-for-jsconf-eu-3gdj>

JSConf EU 可以说是今年最重要的 JavaScript 会议。在 JSConf EU 上已经宣布了许多重要的技术，例如 Node.js，来自全球各地的众多开发人员都将参加。微软赞助了今年的会议，我们带来了一些有趣的东西:一个 LED 供电的艺术品，你可以在展台上控制它，并使用你自己定制的无服务器实现！

这一系列博客文章将深入探讨我们是如何构建这个项目的。

在我从燃烧的人和相关事件的经历中受到启发后，我参与 LED 艺术已经有一段时间了。我已经为可编程 LED 艺术开发了[无线照明控制系统](https://github.com/nebrius/raver-lights/)好几年了，在这一点上，它非常坚固。这是我们展位代码的完美基础。自从我为这个项目创建基础设施以来，我就担任了这个项目的领导角色，我很幸运地与我出色的队友[扬·申克](https://dev.to/jansche)、[坦尼娅·扬卡](https://dev.to/shehackspurple)、[蒂尔尼·西伦](https://dev.to/bnb)和[苏兹·辛顿](https://dev.to/noopkat)一起工作，让它变得生动起来。如果你对这个系统很好奇，这里有一个技术演示:

[https://www.youtube.com/embed/nYG6840g4mw](https://www.youtube.com/embed/nYG6840g4mw)

我为 JSConf EU 设计了一件艺术作品，灵感大致来自竹林。这种设备将具有多个 LED“拍摄”,从云中控制。

[![LED piece CAD rendering](img/db6cceeb6fb4d06355804dc90cdff626.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_YiUB5MH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/333veu5w6ie9yjgmf1bh.png)

这件作品使用了我最初为燃烧的人设计的定制硬件，基于 [ESP8266 12-S WiFi 启用微控制器](https://en.wikipedia.org/wiki/ESP8266)。这些小板子自己控制灯光，并通过 WiFi 连接到树莓派。Raspberry Pi 将使用 [Azure 物联网中心](https://docs.microsoft.com/en-us/azure/iot-edge/?WT.mc_id=devto-blog-brhugh)连接到 Azure，从那里我们可以连接到各种云技术！

该系统由三组代码组成:浏览器 UI 代码、Azure 函数形式的服务器代码和运行在 Raspberry Pi 上的控制器代码。这是完整的架构图(不包括 ESP8266s):

[![Architecture Diagram](img/587fb50d095e02bcb034b80da083274d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oSq6YPm9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/duro6k76csfhbvdbulkx.png)

浏览器 UI 代码由两个客户端组成。第一个客户端将在展台的平板电脑上运行，并将运行新的基于铬的 Edge 版本。这个客户端将允许人们走到展台前，给他们对动画的基本控制。不过这个接口将被有意地限制。对于更高级的动画，我们将有另一个网络用户界面，允许人们提交自定义动画，使用他们自己的 [Azure 功能](https://azure.microsoft.com/en-us/services/functions/?WT.mc_id=devto-blog-brhugh)实现，在那里他们可以完全控制动画。

服务器代码由四个 Azure 函数组成。一个 Azure 函数将实现提交基本动画的端点，另一个将实现提交自定义动画的端点。另一个 Azure 函数将用于返回动画的当前队列，因此人们可以看到他们的动画何时运行。最终的 Azure 函数将在计时器上运行，并使用 [Azure IoT Hub](https://docs.microsoft.com/en-us/azure/iot-edge/?WT.mc_id=devto-blog-brhugh) 将下一个动画从队列中取出并发送到 Raspberry Pi 进行显示。动画队列是使用 [Azure 存储队列](https://docs.microsoft.com/en-us/azure/storage/queues/storage-nodejs-how-to-use-queues?WT.mc_id=devto-blog-brhugh)实现的。使用动画队列的一个好的副作用是我们根本不需要数据库！这简化了代码*和*提高了用户数据的安全性和隐私性(如果它不存在，就没有什么可泄露的)。

最后一部分是运行在 Raspberry Pi 上的控制器代码。这段代码是最小的一段代码，主要是将物联网集线器粘贴到我编写的用于与 ESP8266s 通信的 [RVL 节点](https://github.com/nebrius/RVL-Node)包中。

这个项目的所有代码都是使用[类型脚本](https://www.typescriptlang.org/)编写的。web UIs 也是用 [React.js](https://reactjs.org/) 和 [Redux](https://redux.js.org/) 编写的。RVL 节点模块是结合了 Node.js 方面的 TypeScript 和逻辑方面的 C++通过 Web Assembly 编写的。

所有这些都应该创造一个有趣的，独特的展位体验。我等不及要给你们看了！