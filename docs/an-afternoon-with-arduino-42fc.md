# 与 Arduino 共度的一个下午

> 原文：<https://dev.to/whatrocks/an-afternoon-with-arduino-42fc>

在过去的八年左右的时间里，我一直带着一个 Arduino T1(不是真的在我身上，而是，你知道，在我的宝藏中)，等待合适的时间开始摆弄它。

事实证明，那是昨天下午。这篇文章概述了我到目前为止学到的一些东西。

### Ard-what-now？

Arduino 是一个**微控制器**板。该板包含一个 CPU(中央处理器)以及一些 I/O(输入/输出)连接。你可以把它想象成一个小电路——一个恰好包含一个芯片上的可编程计算机的电路。一个你可以轻松学会编程的芯片。

这是我的 Arduino(Uno 品种的)的照片:

[![Uno](img/5d5a04147e232f7228ad962602130429.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pl03vCIL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4fvs83wn7ftsc3et62cz.jpg)

我喜欢左上角的意大利制造标志。

微处理器(计算机芯片)是靠近设备右下角的长而扁平的黑色矩形。它有 28 个引脚(每侧 14 个)。沿着 Arduino 的顶部边缘，你可以看到一条黑色的输入引脚——这些是 Arduino 的“数字”引脚(这意味着它们可以是“开”或“关”)。在右下方的 CPU 下方，您可以看到另外 5 个“模拟”引脚(这意味着它们可以接收模拟/连续信号)，然后是左侧的几个“电源”引脚，用于提供电压、接地和其他一些我还不知道的东西。

#### Arduino vs 树莓派

此时，你可能会问自己，“Arduino 和[树莓派](https://www.raspberrypi.org/)有什么不同？”

这是一个很好的问题，因为这两款电脑都很便宜，可爱，小巧，你可以花 30 美元或更少的钱买到。但是树莓派是一台全功能的 Linux 电脑。Arduino 是...不是。相反，Arduino 电脑一次只能保存一个程序。它将这个程序存储在持久存储器中，这样你就可以打开和关闭 Arduino，它仍然会“记住”它的最新程序。它更像是一个单一用途的设备——除了你可以随心所欲地设计和构建这个单一用途的设备。

#### 开源根

Arduino 背后的公司是非盈利的，Arduino 本身是开源的——这意味着任何人都可以自己构建 Arduino 板。Arduino 背后的最初想法是制造一个简单的设备，设计师和艺术家可以用它来快速原型化使用**传感器**(又名*输入*，如键盘或鼠标或运动探测器)和**致动器**(又名*输出*，如显示器或打印机或灯)与我们人类互动和交流的*物理计算*项目。酷毙了。

Arduino 与[处理社区](https://processing.org/)紧密相连。事实上，这有点轻描淡写，因为你实际上是在为 Arduino 编写程序时编写处理代码——就像在处理中一样，这些程序也被称为草图。我很高兴看到这一点，因为我最早的计算课程之一是 [Jer Thorp](https://twitter.com/blprnt) 的[加工过程介绍](http://blog.blprnt.com/workshops)(顺便说一下，我毫不掩饰地推荐它)。

正如我提到的，任何人都可以下载 Arduino 的开源原理图，并用基本组件自己搭建一个电路板。但是如果你想让你的修补生活更容易，那么我建议从零售商那里买一个预先组装好的 Arduino，比如 maker shed T1 或 T2 Adafruit T3。我购买的套件(八年前)是[品牌:Arduino 套件](https://www.makershed.com/products/make-getting-started-with-arduino-kit-special-edition)入门——当前版本(v3)似乎零售价为 79.99 美元。IMHO，这绝对值得——该工具包附带了一堆可以帮助你立即开始的好东西，如试验板、彩色电线、可点击的开关、led、传感器和一本友好的入门书籍。我还拿起了 [Maik Schmidt](https://twitter.com/maik_schmidt) 写的 [Arduino:快速入门指南](https://www.amazon.com/Arduino-Quick-Start-Guide-Quick-start-Guides/dp/1934356662)，我也很喜欢这本书。

### 趣味 led

我相信法律要求你写一个程序，让 LED 闪烁，作为你的第一个 Arduino 项目。

如果你几天前问我关于 led 的问题，当然，我知道 led。我的游戏机里的那些小红灯。代表...发光...二极管。

太好了，你继续，什么是二极管？

嗯。

这已经是玩 Arduino 的乐趣之一了。有各种各样的基本电子产品，我多少知道一些，但无法向一个五岁的孩子或我桌子上的一只橡胶鸭子解释。或者根本不知道。但是 Arduino 帮助我以一种切实可行的方式解决了这些问题。

那么，让我们一起来看看一个 LED。

[![LED](img/8c81d9cecc0e8c8c88bdf2a8e297df23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2C04UHJU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aq63yj6k7zmgbbn62d6y.jpg)

看到红色部分伸出的较长的大头针/腿了吗？那是**阳极**端子。阳极是 LED 的正极端。较短的那条腿是**阴极**——负端。当连接时，电子将从阳极流向阴极。你需要将正极连接到提供电压的物体上，负极需要接地。所有二极管都是极化的，这意味着它们有这些截然不同的正负两面。当连接到有源电路时，led(发光二极管)恰好提供照明。

它们也可以是许多漂亮的颜色。

好了，这是我们的法律要求草图，每半秒闪烁一次连接到数字引脚 13 的 LED。

```
const int LED = 13;

void setup() {
    pinMode(LED, OUTPUT);
}

void loop() {
    digitalWrite(LED, HIGH);
    delay(500);
    digitalWrite(LED, LOW);
    delay(500);
} 
```

很简单，对吧？我们首先为正在使用的 pin 声明一个常量变量。在`loop()`开始它的无限循环之前，`setup()`函数将为每个程序运行一次，所以我们只需让 Arduino 知道我们想要将管脚 13 设置为`OUTPUT`模式。然后在我们的无限循环中，我们将通过使用`digitalWrite`函数将`HIGH` (5 伏)或`LOW` (0 伏)传递到我们的引脚 13 来切换引脚的电压，在这些操作之间暂停 500 毫秒。

如果您来自处理领域，那么这个`setup()`和`loop()`的程序结构应该看起来非常熟悉，因为它实际上是相同的。

Arduino IDE 提供了一种简单的方法来验证你的程序编译，然后再刷新到你实际的 Arduino 上，所以我建议你先点击`Verify`按钮。这将捕捉语法错误，比如讨厌的分号丢失。

接下来，我们可以设置我们的物理设备。

我将 LED 插入 Arduino，阳极引脚插入 13 号引脚，阴极引脚接地。请注意，引脚 13 是 Arduino 上的一个特殊引脚，它内置了一个电阻。如果您尝试使用 Arduino 的任何其他引脚，则 LED 会烧坏。

最后，我们可以通过单击 IDE 中的`Upload`按钮，通过 USB 连接将我们的程序从计算机发送到 Arduino。一旦完成，你的 Arduino 应该会快乐地闪烁，然后它就可以去参加无限的比赛了。

[![Binary Counting](img/b001b328096a8a6784f0a16cc8b2e41e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oG733p87--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/504ctt601fl3mxoffpu2.gif)

看那个闪光灯。很棒，是吧？请注意，这张 gif 图确实加快了一些速度。

### 将“S”放入 USB

因此，当我继续构建东西的时候，我不可避免地发现自己想要彻底摆脱一个不起作用的程序。

让我们来谈谈用 Arduino 打印东西。

您的 Arduino 通过 USB 电缆连接到电脑。USB。USB。那一定代表着什么，对吧？确实如此。它代表“通用串行总线”USB 是计算机和外围设备之间通信的“行业标准”。如果你回想一下惊奇队长时代甚至更早的时候，你可能会记得我们将外设连接到计算机的其他方式——比如点阵打印机的并行端口或 PS/2 键盘端口。自从卡罗尔·丹弗斯把我们留在这里让我们自生自灭以来，USB 已经占据了我们的心灵、思想和钱包。但是当我们使用 USB 设备时，我们仍然使用“串行连接”——所以我们需要使用串行协议来与我们的 Arduino 通信。

换句话说，如果我们想要发送或接收来自 Arduino 程序的信息，我们需要与设备建立一个串行连接。以下是在加工草图中如何做到这一点:

```
const unsigned int BAUD_RATE = 9600;

void setup() {
    Serial.begin(BAUD_RATE);
}

void loop() {
    Serial.println("Hello, world!");
} 
```

波特率，嗯？我也知道这个术语。调制解调器有波特率，IIRC。一些维基百科和谷歌显示波特率是信息在串行通道中传输的速率。在这种情况下，波特率为 9600，我们每秒最多传输 9600 位。9600 恰好是 Arduinos 的标准波特率，但我相信你可以选择不同的速率。

要查看您的“控制台”，您可以单击 IDE 中的“串行监视器”按钮。

[![Hello](img/c84a550e3780891ef19d8c4ef8eaa050.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---A2t3TtU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ynkzzujsyg0lpk5cmo4h.png)

除了查看收到的信息，您还可以使用顶部面板上的文本输入和`Send`按钮将信息发送回本显示器中的 Arduino。例如，您可以编写一个程序，根据特定的输入键打开或关闭 led。

如果你不想在 Arduino IDE 中使用串行监视器怎么办？也许是时候让旧的方式消亡了。我同意。如果你在 Mac 上，那么你可以试着从你的终端运行`screen`命令，指定你的 Arduino 串行连接的名称和波特率。

```
screen <name_of_serial_connection> 9600 
```

在我的例子中，连接的名称是`/dev/cu.usbmodem14101`，可以在 Arduino IDE 的`Tools/Port`菜单中找到。

不过，给你个警告。如果您关闭此终端窗口，它不会关闭会话，并且您将无法向 Arduino 上传新程序。这叫“脱屏”，很烦。您需要以某种方式退出屏幕，可以使用以下命令:

```
screen -X -S <name_of_session> quit 
```

哦，要获得分离会话的名称，您可以键入:

```
screen -ls . 
```

这整个串行通信的事情打开了一些有趣的想法，因为你可以有你的 Arduino 和其他东西之间的双向通信。忘了阿利克夏吧。不是-好吧，谷歌一下。走开，Siri。现在，你可以建立自己的会说话的机器人最好的朋友。希望对你也有所帮助。

### 用发光二极管二进制计数

总的来说，就人生目标而言，我一直试图更好地用二进制思考和计数，所以我决定为我的下一个 Arduino 项目构建一个小的二进制计数器。

```
const unsigned int LED_BIT0 = 12;
const unsigned int LED_BIT1 = 11;
const unsigned int LED_BIT2 = 10;
const unsigned int LED_BIT3 = 9;

long result = 0;

void setup() {
  pinMode(LED_BIT0, OUTPUT);
  pinMode(LED_BIT1, OUTPUT);
  pinMode(LED_BIT2, OUTPUT);
  pinMode(LED_BIT3, OUTPUT);
}

void loop() {
  result++;
  if (result == 16) {
    result = 0;
  }
  output_result(result);
  delay(500);
}

void output_result(const long result) {
  digitalWrite(LED_BIT0, result & B0001);
  digitalWrite(LED_BIT1, result & B0010);
  digitalWrite(LED_BIT2, result & B0100);
  digitalWrite(LED_BIT3, result & B1000);
} 
```

[![Binary Counting](img/de059a08cbcdf47a870a4570675230f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6VuxUUHx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jxxawvo5qm6lwk2gv75.gif)

我不知道为什么红色 LED 没有其他三个 LED 亮。我试图用另一个 LED 更换它，但没有成功。但是，嘿，除此之外，这东西还能用！

我还了解到面包板很棒。能够将 led 的所有阴极侧连接到试验板的底部负极行，然后只将该行连接到 Arduino 的接地端口一次是非常有用的。当然，在这里我有更多要学习和欣赏的东西。

此外，这是我第一次真正利用按位 and 运算符的威力。我将我的`result`与一个二进制数进行位“与”运算，该二进制数代表每个 led 的二进制数字(1、2、4、8)。如果`result`和我们的二进制数对于给定的二进制数字都包含一个`1`，则位与运算返回`true`。例如，让我们看看数字`3`

```
3 & B0001; // true
3 & B0010; // true
3 & B0100; // false
3 & B1000; // false 
```

这里最后一个窍门是，`digitalWrite`函数将`true`布尔值转换成`HIGH`(打开 LED)，将`false`转换成`LOW`(关闭 LED)。因此，对于数字`3`，用于`1`数字和`2`数字的 LED 应该点亮，而`4`和`8`应该熄灭。

这非常棒，使得代码非常简洁。对我来说，这里有更多值得探索的东西。

### 多修修补补

所以，仅仅过了一个下午，我就学到了很多东西，而且一路上也很开心。

你会问，下一步是什么？嗯，电阻仍然令人费解。我还不确定如何确定特定情况下需要多大程度的抵抗。我已经烧坏了一个 LED 灯(在它生命的最后一口气中，一缕令人愉快的烟冒出来)，可能就是这个原因。也真的很难去阅读那些彩色的条带来试图确定它们的阻力水平。这似乎是一个很棒的小型计算机视觉/深度学习应用程序。或者也许我应该更经常地使用我的万用表。

我也在思考模拟信号和数字信号的区别。数字是二进制的(开或关)，而模拟是连续的。我们在生活中观察到的大部分都是模拟信号。因此，当我们选择对它们进行数字化处理时，我们需要选择特定的时刻来对连续信号的值进行“采样”。施密特的书解释说，音频 CD 每秒 44，100 次采样(或 44.1 kHz)。也许这就是黑胶回来的原因。

我在想，我和 Arduino 的下一个明显的项目是用二进制数字做一个闹钟。网上有很多这个项目的好例子，我认为这是一个学习/提高我的焊接技能和快速心算二进制数的好方法，尤其是在半夜昏昏沉沉的时候。