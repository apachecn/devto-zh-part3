# 物联网——从零到 DIY 比特币股票

> 原文：<https://dev.to/hector6872/internet-of-things--from-zero-to-diy-bitcoin-ticker-2d1k>

## TL；【T2 博士】源代码 & [图库](https://imgur.com/gallery/Yuvjj67)

我相信几乎每个人都至少听说过一次 **[Arduino](https://www.arduino.cc/)** 和/或 **[树莓派](https://www.raspberrypi.org/)** *低价电脑*。由于它们的低价格和小的信用卡大小的形状因子(如果我们参考 [Raspberry Pi Zero](https://www.arrow.com/en/research-and-events/articles/raspberry-pi-3-vs-raspberry-pi-zero-w) 模型，甚至更小),两者都被用于无数巧妙的 **DIY** [电子](https://create.arduino.cc/projecthub) [项目](https://www.raspberrypi.org/forums/viewforum.php?f=15&sid=95585e4b93a16bf8879382a5025493a4)中，或者通过使用许多可用的扩展模块中的一个。

但他们不是唯一的(事实上有很多):其中最受欢迎的是 **[NodeMCU](http://www.nodemcu.com/index_en.html)** *又名 Arduino 黑仔*🏆(基于**[ESP8266](https://www.espressif.com/en/products/hardware/esp8266ex/overview)**wifi-[SOC](https://en.wikipedia.org/wiki/System_on_a_chip))

### 它们有什么区别？

它们是[完全不同的概念](https://cybergibbons.com/uncategorized/arduino-misconceptions-4-the-arduino-is-obsolete-now-the-raspberry-pi-exists/),因为它们被构想来执行不同的任务；而 **Arduino** 实际上是一个[几乎坚不可摧的](https://www.rugged-circuits.com/10-ways-to-destroy-an-arduino/)开源**微控制器**,我们可以使用一个简单的脚本(在 C/C++中；一次一个应用程序)从第一分钟开始， **Raspberry Pi** 就是一个*完整的* **小型计算机**在一个小主板上( [SBC](https://en.wikipedia.org/wiki/Single-board_computer) )，运行一个专用的 [Linux 操作系统发行版](https://www.raspberrypi.org/downloads/)(因此它可以使用多种语言进行编程)，这使得我们可以运行不同的程序，就像我们在台式计算机上一样。

> “如果你的应用更多的是控制事物，Arduino 可能是一个更好的选择。而如果你需要处理大量数据，PI 是你的最佳选择"
> — [Arduino 与 Raspberry Pi，哪个最好？](https://www.youtube.com/watch?v=7vhvnaWUZjE)

也就是说，更像是一个微控制器而不是一个小型机，我们有我们的英雄 **[NodeMCU](https://github.com/nodemcu/nodemcu-devkit) :一个 Arduino 兼容 [dev](https://github.com/esp8266/Arduino) 板，内置 WIFI，价格[不到 5 美元](https://www.aliexpress.com/item/ESP8266-CH340G-NodeMcu-Lua-V3-ESP8266-CP2102-NodeMcu-Lua-V2-Wireless-Module-ESP8266-ESP-12E-Micro/32909262615.html)** ，还支持 [Lua](https://www.lua.org/) 脚本语言(有几个 **ESP8266** 芯片组的变体，但强烈建议从[第二代](https://frightanic.com/iot/comparison-of-esp8266-nodemcu-development-boards/) **ESP-12E 购买**

## Arduino IDE 上的  NodeMCU

Arduino 平台有自己的 **[IDE](https://www.arduino.cc/en/Main/Software)** (实际上更像是编辑器而不是 [IDE](https://imgflip.com/i/2nzd3m) )免费提供，并且很容易使其与 NodeMCU 兼容:

1.  根据您机器的操作系统，按照这些说明安装独立的 Arduino IDE

2.  启动 Arduino IDE

*   打开*偏好设置*

*   将链接[http://arduino . esp8266 . com/stable/package _ esp8266 com _ index . JSON](http://arduino.esp8266.com/stable/package_esp8266com_index.json)添加到*附加董事会管理器 URL*

*   打开*板卡管理器…* (菜单*工具* > *板卡…* )

*   搜索并安装 *ESP8266*

*   打开菜单*工具* > *板卡* …选择*节点 MCU [x.x…](https://frightanic.com/iot/comparison-of-esp8266-nodemcu-development-boards/)*

搞定了。

### 你好世界！

现在我们的开发环境已经准备好了，让我们用一个最简单的[例子来验证一切是否正常，包括](https://www.arduino.cc/en/Tutorial/BuiltInExamples) : **[LED 闪烁](https://www.arduino.cc/en/Tutorial/BlinkWithoutDelay)** 。

> 我们将使用 stock " [Arduino language](https://www.arduino.cc/en/Main/FAQ#toc13) "(将项目限制为一个文件。ino extension 被称为“sketch”)，它基本上是一组 C/C++函数(在我看来，他们在抽象不同的接口方面做得非常出色，因为即使是初学者也能够轻松阅读和理解几乎所有的代码)。高级用例可能需要一个*真正的* IDE 加上普通 C/C++语言的使用。

1.  启动 Arduino IDE

2.  将电路板连接到计算机(通常在背面贴上胶带，以确保没有金属碎片会使电路板短路)

3.  为您的串行适配器选择正确的`COM`端口(*工具* > *端口…* )⁴)

4.  加载*闪现*草图(*文件* > *实例*>*ESP8266*>*闪现*)。这里没有什么要解释的，因为我们可以看到代码对 understand⁵.来说非常容易

5.  将下面一行添加到示例的最开头(因为该示例是为较旧的模块版本而不是为 ESP-12 编写的):`#define LED_BUILTIN 2`

6.  上传并等待几秒钟(上传过程中 led 应该快速闪烁)

欢迎来到生活！

### 串行监视器

才能做出真正的“你好，世界！”示例我们将修改前面的 Blink 示例来打印这个众所周知的消息。

1.  在`setup()`方法中添加`Serial.begin(9600); Serial.print(“Hello, World”);`行

2.  上传吧

3.  打开*串口监视器* ( *工具* > *串口监视器*)

4.  在下拉列表中选择 *9600 波特*选项

5.  按下板上的*复位*按钮

[测井](https://www.youtube.com/watch?v=iNkrF43SZEU)！

## 谁在那里？

正如我们之前所看到的，我们的微小且非常便宜的 **NodeMCU 内置了 WIFI 芯片组**。因此，我们要做的第二件事是在*串行监视器*中显示我们周围所有可用的无线网络，再次使用其中一个包含的示例: *WifiScan* ( *文件* > *示例*>*esp 8266 wifi*>*wifi scan*)。

> 确保在代码(`Serial.begin(xxx);`)中设置与串行监视器下拉列表中相同的波特率。

### 家是 WIFI 连接的地方

这一次没有例子向我们展示如何连接到我们可爱的互联网，所以我们将创建一个新的脚本(*文件* > *新*)包含下面的代码(我们可以看到代码几乎是不言自明的，不需要任何额外的注释🤞):