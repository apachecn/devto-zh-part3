# 使用高速 AVR 定时器构建 Arduino 照片闪光测试仪

> 原文：<https://dev.to/ascorbic/build-an-arduino-photo-flash-tester-using-high-speed-avr-timers-5gko>

我为相机闪光灯制作了一个低成本、高速计时器，作为 Arduino 的挡箭牌。除了对摄影师有用之外，这也是在 Arduino 上使用高速、精确的 AVR 定时器、中断和输入捕捉的有趣练习。不包括 Arduino 本身，总成本约为 3 美元。

这是一个很长的帖子。第一部分是关于摄影，第二部分是关于电子，第三部分是关于编写 Arduino 和 AVR 代码。随意跳过任何你不感兴趣的部分！

## 为什么闪速很重要？

当我们谈论闪存速度时，我们可能指的是两种不同的东西。首先是滞后，或延迟。这是 hotshoe 或同步电缆上的触发信号与闪光灯发出光之间的时间。这类似于照相机的快门延迟。另一个是脉冲宽度，即灯发光的时间。这类似于快门速度，并结合闪光灯的亮度给出曝光。当你改变闪光灯的“功率”时，它实际上并没有改变闪光的亮度，只是改变了脉冲宽度。这就是为什么高速摄影时，你需要将闪光灯调到最低功率。

对于大多数高速摄影来说，只要保持一致，滞后不是主要问题。如果你正在捕捉一颗子弹，你可以通过简单地移动相机远离枪来补偿滞后。如果不一致，那就更成问题了，因为这使得很难对准镜头。真正重要的是脉冲宽度。脉冲越长，你拍摄的照片就越模糊。当然，你可以通过反复试验来测试，但如果我们先测量它，会容易得多，因为这意味着我们可以很容易地计算出我们可以用闪光灯捕捉到的物体的速度。

## 测量闪光速度

开发世界上最快的相机闪光灯意味着我花了很多时间测试闪光灯有多快。我使用连接到示波器的 Thorlabs 光电探测器来实现这一点，它的优点是非常精确，可以给出亮度随时间变化的完整曲线。氙闪光灯具有独特的闪光，最初是明亮的尖峰，随后是平稳期(其长度取决于闪光功率)，随后是电源关闭后余辉的缓慢衰减。这种行为限制了氙闪光灯的最高速度。来自设计良好的 LED 闪光灯的脉冲将具有大致方形的轮廓。

[![a xenon flash](img/306e1fa4f8ebf21a6a16a0e78e407570.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8OpgWeFi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/5549e27ae4b058b787452b7c/55536ffde4b05092eb24531f/55536ffee4b0516ce940eecc/1431531531816/1-32-xenon.png%3Fformat%3D750w)

*来自氙闪光灯的脉冲*

[![LED flash](img/11d353dfaa933288b2cb303c619356f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ALeCX8tN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/5549e27ae4b058b787452b7c/55536ffde4b05092eb24531f/55536ffee4b03971c5ba7c4d/1431531545317/3us-vela.png%3Fformat%3D750w)

*来自 LED 闪光灯的脉冲*

# 预算上的闪光速度测试

一个光电探测器要花掉你 100 美元甚至更多，一个示波器要贵几百美元。如果你还没有这些，对于大多数人来说，如果他们只是想检查自己的闪光灯并与其他人进行比较，这就太贵了。我决定建立一个基于 Arduino 的预算版本。对于实际的检测器，光电二极管比光敏电阻更加灵敏、精确和灵敏，因此我们将使用它。Thorlabs 光电探测器中使用的光电二极管价格约为 30 美元，但这对我们来说太贵了。我用了一个[欧司朗 SFH 203 光电二极管](https://octopart.com/sfh+203-osram+opto-31094946)，大概 50c 左右。确保你买的是对可见光而不是红外线敏感的。很容易看出区别:红外的是黑色(或者更确切地说是红外线)包装，而可见光的是透明的。我们通过施加反向偏压使光电二极管工作在光电导模式。通过它测得的电流表示照射在二极管上的光的亮度。我们添加了一个 RC 滤波器，以避免高频噪声干扰我们的读数。一个 50 欧姆的负载电阻为我们提供了一个用 Arduino 测量的电压。

[![The Arduino shield](img/9a926dcdda3c1ad58909e3db5bd9edfb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lHwy8SC3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/5549e27ae4b058b787452b7c/t/55537051e4b01be7fab2998d/1431531639757/%3Fformat%3D750w)

我们会把它建成 Arduino 盾牌。你可以使用 protoshield，但我只是用了一个带一些引脚的小 PCB。如果我们想测量滞后，我们还需要触发闪光，这样我们就可以测量触发和闪光之间的时间。我为此增加了一个 3.5 毫米的音频插座。一些闪光灯(包括 [Vela One](https://www.vela.io/vela-one-high-speed-flash) )有 3.5 毫米的触发插座，所以对于那些简单的音频电缆就足够了。对于其他人，你需要一个 hotshoe 或某种 PC 同步适配器。一个按钮触发。要获得脉冲宽度的读数，你只需将闪光灯对准探测器，然后发射。它将串行打印脉冲宽度。如果你也想测量滞后，那么你需要把闪光灯接到屏蔽罩上，对准探测器，然后按下屏蔽罩上的按钮。然后你将通过串行得到这两个值。

[![circuit diagram](img/7f93e9903914cc4fd206e0aaa4e8797d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ez7r_Jjf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/5549e27ae4b058b787452b7c/t/555370d3e4b03288444d9aeb/1431531741252/%3Fformat%3D750w)

## 布线完毕

如上所述，我们将通过测量光电二极管的电压来检测闪光。Arduino 程序员可能在想，我即将爆发 analogRead。不幸的是，这太慢了。我们不仅需要快速读数，还需要精确的计时器，标准的 Arduino 计时器也不够好。我们需要使用本地 AVR 代码。我正在用一台使用 ATMega32u4 微处理器的莱昂纳多开发这个。我将直接使用微处理器的寄存器，所以代码是达芬奇特有的。稍加改动，它应该也能在其他 Arduinos 上运行，但它不会开箱即用。

ATMega 芯片有一个内置的模拟比较器，非常适合这种情况。它测量一个引脚上的电压是高于还是低于基准电压，并相应地产生中断或设置寄存器。基准电压可以是微处理器的内部基准电压，也可以是外部引脚上的电压。我们将使用后者，这样我们就可以设置阈值。一旦光电二极管的电压超过该阈值，就会触发中断。用示波器做了一点试验和误差显示，100-200 毫伏是一个很好的阈值，所以我用一个分压器来产生这个。Arduino 网站有一个有用的[页面，显示了莱昂纳多](https://www.arduino.cc/en/Hacking/PinMapping32u4)的引脚映射。由此可以看出，模拟比较器输入引脚 AIN0 映射到 Arduino 数字引脚 7。32u4 使用 ADC 多路复用器作为基准电压，因此我们将使用 ADC0，它是 Arduino 世界中的模拟输入 5。我们将按钮放在中断引脚上，在本例中，INT0 是数字 3。我们将通过数字引脚 4 上的 MOSFET 触发闪光。

## 概述

顺序如下:用户按下按钮，触发外部中断。在中断处理程序中，我们打开 flash trigger 引脚，记录定时器 1 的当前时间，然后启动定时器 4。定时器 4 在 40 s 后溢出，此时我们关闭 flash trigger 引脚。另外，模拟比较器会持续观察来自光电二极管的脉冲。这只会被闪光灯触发，因为其他任何东西都不够亮。当检测到脉冲时，它将当前时间存储在输入捕获寄存器中，并触发输入捕获中断。在输入捕获处理器中，我们存储脉冲开始时间，复位定时器，并翻转比较器，使其在下降而不是上升时触发。闪光结束后，脉冲的下降沿再次触发中断，并将时间存储在输入捕捉寄存器中。在该处理程序中，我们设置了一个标志，表示一次刷新刚刚结束，并再次翻转中断以观察新的刷新。在主循环中，我们看到一个 flash 标志已经置位，并从输入捕获寄存器中获取值。该值是上升沿和下降沿之间的时钟周期数:即脉冲宽度。如果触发时间已经设置，那么这意味着闪光是由我们触发的，我们也可以计算滞后。我们将这些值转换成微秒并通过串行打印出来。

## 代码

正如我之前所说的，我不得不使用本地 AVR 代码，因为我们需要访问更快的定时器和中断，但这可以与常规 Arduino 代码混合在一起。如果你不熟悉 AVR 寄存器，这可能看起来有点奇怪，但我已经试图彻底评论它。大多数看起来奇怪的东西是在寄存器上设置位，这就是你如何配置微控制器。这些值都可以在处理器的数据手册中找到。我花了很多时间阅读 AVR 数据表，对于任何 Arduino 程序员来说，它们都是有用信息的金矿，如果你开始直接使用处理器，它们也是至关重要的。

我们用了两个计时器。一个是脉冲宽度和滞后的定时:即重要的东西。我们使用另一个定时器来完成向闪光灯发送触发信号、去抖、复位延迟和各种其他日常事务。首先，我们将设置模拟比较器。我们将使用输入捕捉，因为它可以在电压越过阈值的确切时刻获取计时器的值。我们将在 Arduino 设置功能中设置这些。

```
void setup() {

    //Disable interrupts while we're fiddling with settings.
    cli();

    //Timer4 is our flash trigger pulse and housekeeping timer.
    //Enable overflow and output compare interrupts. Start turned off.
    TIMSK4 = _BV(TOIE4);
    TCCR4A = 0;
    TCCR4B = 0;
    OCR4A  = 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

这没什么意思，让我们来看看模拟比较器。我们使用定时器 1，它通常由 Arduino 伺服库使用。我们这里没有使用伺服系统，所以我们很好。我们需要根据需要重新配置。

```
 //Mode 0: normal mode, no port outputs. 
    TCCR1A = 0;

    //This sets the clock to no prescale (16MHz).
    //Trigger on rising edge. 

    TCCR1B = _BV(CS10) | _BV(ICES1);

    //Enable analog comparator input capture.
    ACSR = _BV(ACIC);

    //Clear input capture interrupt flag - thanks Don
    TIFR1 = _BV(ICF1);

    //Enable input capture interrupts
    TIMSK1  |= _BV(ICIE1);

    //Use multiplexer for our reference voltage.
    //Defaults to ADC0, i.e. PF0/Analog 5
    ADCSRB |= _BV(ACME);

    //Turn off the ADC as we'll be using its multiplexer.
    ADCSRA &= ~_BV(ADEN);

    //Turn off digital input on the comparator negative input (PE6) 
    DIDR1 |= _BV(AIN0D); 
```

Enter fullscreen mode Exit fullscreen mode

我们将定时器 1 设置为 16MHz。当模拟比较器引脚上的电压升至阈值以上时，当前定时器值存储在寄存器中，并调用中断处理程序。让我们编写处理程序。

32u4 让你选择是在上升还是下降时触发处理器，但恼人的是不能两者都触发。我们需要检测脉冲的两个边沿，这样我们就可以检查它有多长，所以我们需要一个解决方法。这意味着我们需要首先在上升时触发，然后在中断处理程序中切换到下降时触发，这样我们就可以捕捉到脉冲的末端。

```
// Input capture interrupt
ISR(TIMER1_CAPT_vect) {

    if(bit_is_set(TCCR1B, ICES1)) {
        //We're currently triggering on rise. Change to fall
        TCCR1B &= ~_BV(ICES1);
        //Reset counter
        TCNT1 -= ICR1;
        flashStart = ICR1;
    } else {
        //We're triggering on fall. Change to rise.
        TCCR1B |= _BV(ICES1);
        flash = 1;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

当我们被上升沿(表示一个闪光脉冲的开始)触发时，我们设置记录脉冲开始时间的 flashStart 值。这是用来衡量滞后的。当脉冲开始时，我们重置计时器的计数器，这样我们就可以读取 fall 处理程序中的值来获得脉冲的长度。在 fall 处理程序中，我们简单地设置了一个在循环中读取的标志。这是因为在一个中断处理程序中做太多是一个坏主意，我们当然不应该做任何串行通信。

我省略了按钮引脚的设置，但我们只是将其设置为下降时中断输入，并使能内部上拉电阻。这是负责人。

```
// Start button pressed
ISR(INT0_vect) {

    if(ignore == 1) {
        return;
    }

    ignore = 1;
    digitalWrite(FLASH_PIN, HIGH);
    triggerTime = TCNT1;
    //Reset and start timer4 with no prescaler
    TCNT4 = 0;
    TCCR4B = _BV(CS40);
    triggering = 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们做的第一件事是检查“忽略”，这是一个反跳标志。如果我们通过了，我们就触发闪光灯并记录下当前时间。我们启动定时器 4，它将在 40 微秒后触发信号第一次溢出时停止触发信号。这是一个足够长的脉冲来触发闪光。这是定时器每次溢出时触发的处理程序。

```
// Timer overflow
ISR(TIMER4_OVF_vect) {
    if(triggering == 1) {
        //Turn off the trigger pulse after 1 overflow (~40us)
        digitalWrite(FLASH_PIN, LOW);
    } else if(triggering == 100) {
        //Reset everything after 100 overflows (~4ms)
        //Turn off timer4
        TCCR4B = 0;
        ignore = 0;
        triggerTime = 0;
    }
    triggering++;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们用一个计数器来查看溢出了多少次。第一次溢出时，我们关闭闪光灯。在 100 次溢出(大约 4 毫秒)后，我们关闭计时器，并再次开始监听按钮按压。我们清除 triggerTime 值，因为 4ms 后，我们可以确信按钮没有触发任何闪烁。

只有当我们到达 flash 的末尾，并准备通过串行连接发送结果时，才会使用循环。

```
void loop() {

    if(flash == 1) {
        //We were triggered by the button
        if(triggerTime > 0) {
            Serial.print("Lag ");
            Serial.print(clockCyclesToMicroseconds(flashStart - triggerTime), DEC);
            Serial.println("us");
        }

        Serial.print("Pulse width ");
        Serial.print(clockCyclesToMicroseconds(ICR1), DEC);
        Serial.println("us");

        Serial.println("********");

        ignore = 0;
        flash = 0;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 弊端

32u4 上的模拟比较器不会在两个边沿都触发输入捕捉，这让我们有点受限。我们的解决方法是翻转触发输入捕捉的边沿，以便我们可以捕捉到两个边沿，低至约 3 s，一旦我们处理更短的脉冲，我们似乎会错过下降沿。在我们告诉比较器捕捉它之前，脉冲就结束了。有几种方法可以解决这个问题。一种是使用我们自己的外部比较器。然后，我们可以连接最多两个输入捕捉引脚，分别捕捉上升和下降信号。这应该让我们抓住两边。或者，我们可以改进固件来更快地切换触发边缘，可能通过使用裸中断。

## 结果

那么，结果呢？我测试了三次闪光，并将结果与示波器的结果进行了比较。我在这些测试中使用了两个闪光灯，还有 Vela One 高速闪光灯。

| 闪光 | 环境 | 滞后 | 脉冲宽度 |
| --- | --- | --- | --- |
| Yongnuo YN560-II | 1/128 | 60-62 | 146-158 |
| 奥林巴斯 FL-600R | 1/128 | 77-79 | 128-150 |
| 维拉一号 | 3 秒* | eight | three |

**这不是 Vela One 的最快设置，但测试仪无法处理比这更短的脉冲。真正最短的脉冲是 500ns*

如果你尝试建立这个，请分享你的结果。如果你有一个气隙闪光灯来测试，我会特别感兴趣(尽管你可能会达到上述速度限制)。Github 上的代码是[。如果您对改进代码或设计有任何建议，请提交请求或留下评论。](https://github.com/VelaLabs/flashtester)

四年前，当我在开发 Vela One 的时候，我写了这篇文章。它是现在已经上市的，并且仍然是市场上最快的闪光灯。