# Go:使用 gousb 对 AKAI MPD26 进行逆向工程

> 原文：<https://dev.to/davidsbond/golang-reverse-engineering-an-akai-mpd26-using-gousb-3b49>

### 简介

前几天，我发现了谷歌的 gousb 包。Golang 中用于与 USB 设备交互的低级接口。在写这篇文章的时候，它是独一无二的。我还没有看到许多 golang 软件包试图解决与 USB 设备的接口问题，并渴望尝试一下。

我仔细阅读了我公寓周围的一堆废弃的技术。经过一番深思熟虑后，我决定对一台旧的 [AKAI MPD26](http://www.akaipro.com/products/legacy/mpd-26) 采样器进行逆向工程。这些东西在最初发布时是非常受欢迎的选择。现在，有更好的采样器，功能更丰富的界面。不幸的是，我从未真正深入创作电子音乐/擅长使用采样器。这似乎是一种值得购买的方式。

首先，让我们检查一下我们希望能够读取的采样器的不同部分。它提供:

*   6 个推子，这些用于管理各种频道的音量。你会把这些分配给你的 DAW 中他们可以操纵的东西。
*   6 个旋钮，这些更多的是用于操纵你已经应用到音轨的自动化，但也可以很容易地像推子一样使用，反之亦然
*   16 个压力感应垫，这些用来触发你想听到的声音。

这是一个相当简单的设置。还有很多按钮和旋钮可以修改上述控制的输出。例如，一个“音符重复”按钮，如果在其上保持压力，它将使垫继续触发。

我决定为我希望采样器的界面如何工作设定一些目标:

1.  我想在 Golang 实现它
2.  它应该提供一种使用通道从采样器的各个方面读取值的方法
3.  它应该尽可能地去除与 USB 设备接口的讨厌之处

### 连接到 USB 接口

老实说，我以前从未做过任何与 USB 设备相关的编程工作，所以我真的不知道自己在做什么。幸运的是，gousb 库提供了一个非常简单的接口。然而，它需要一些关于 USB 设备如何工作的背景知识。

图书馆的 godoc 页面对它如何工作有一个很好的解释。我真希望在强行进入之前能先读一下。

#### 决定使用哪个 USB 设备

第一个挑战是找出主机上的哪个 USB 端口实际上连接到了采样器。为此，我们需要知道 usb 设备的产品和供应商标识符。

[这个关于堆栈溢出的问题](https://electronics.stackexchange.com/questions/80815/what-is-a-product-id-in-usb-and-do-i-need-to-buy-it-for-my-project)很好地解释了这些标识符是什么:

> 供应商 ID 或 VID 是一个 16 位的数字，您必须从 USB 基金会购买。如果你想制作 USB 设备(并完全遵守规则), VID 可以识别你的组织。
> 
> 产品 ID 或 PID 也是一个 16 位数字，但受您的控制。当您购买一个 VID 时，您有权将其用于每一个可能的 PID，因此这将为您提供 65536 个可能的 VID:PID 组合。目的是 VID:PID 组合应该在全球范围内唯一地标识特定的产品。

AKAI MPD26 已经有了产品和供应商标识符，那么我们如何找到它们呢？如果您在 UNIX 系统上使用`lsusb`命令，这实际上相当简单。插上设备后，我很容易就能找到它。

```
> lsusb -v 
```

Enter fullscreen mode Exit fullscreen mode

使用这个命令，我能够确定产品和供应商标识符:`0x0078`和`0x09e8`。利用这些，我们可以使用`gousb.Context.OpenDevices()`方法。该方法采用一个参数`func(desc *gousb.DeviceDesc) bool`。对于每一个连接的 USB 设备，提供的方法被执行，如果我们找到了一个我们感兴趣访问的设备，应该返回`true`。

```
const (
  product = 0x0078
  vendor = 0x09e8
)

func example() {
  ctx := gousb.NewContext()
  devices, _ := ctx.OpenDevices(findMPD26(product, vendor))

  // Do something with the device.
}

func findMPD26(product, vendor uint16) func(desc *gousb.DeviceDesc) bool {
  return func(desc *gousb.DeviceDesc) bool {
    return desc.Product == gousb.ID(product) && desc.Vendor == gousb.ID(vendor)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用这段代码，我们得到了一个只有一个元素的设备数组，这个元素就是采样器！

### 从 USB 设备中读取

在处理 USB 设备时，我们需要获得三样东西:配置、接口和端点。

该库将 USB 配置定义为:

> 配置描述符决定了设备上可用 USB 接口的列表。

也定义了接口:

> 每个接口都是物理 USB 设备及其活动配置中的一个虚拟设备。可以有许多接口同时处于活动状态。接口从零开始按顺序枚举。

最后，端点:

> 端点可以被认为类似于 UDP/IP 端口，除了数据传输是单向的。

我们追求的是端点，也就是我们能够从设备读取数据并对其做出反应的地方。为了得到它，我们需要找出正确的配置，获得接口，然后是端点。

由于几个原因，我第一次尝试连接 USB 设备失败了。我尝试使用了一些在`gousb`库中可用的便利方法。主要是`DefaultInterface`和`ActiveConfigNum`方法。

这是`DefaultInterface`的文档:

> 默认接口使用当前活动配置的备用设置#0 打开接口#0。它旨在为具有单一配置、界面和替代设置的最简单界面的设备提供快捷方式。应该调用 done func 来释放声明的接口和配置。

和`ActiveConfigNum`:

> ActiveConfigNum 返回活动配置的配置 id。该值对应于 ConfigInfo。此设备的 ConfigInfos 之一的 Config 字段。

`DefaultInterface`应该允许您跳过寻找合适的配置，这样您就可以直接到达您想要的端点。我不确定这是我的机器的问题，还是设备本身的问题，但是每次都会返回一个错误。我对`ActiveConfigNum`方法也有同样的问题。

然而，当我试图连接到设备时，我得到了下面的错误:

```
libusb: device or resource busy [code -6] 
```

Enter fullscreen mode Exit fullscreen mode

这是因为内核已经为 USB 设备分配了驱动程序。在这种情况下，`pulseaudio`一插上 USB 设备就要求得到它，因为它是一个音频接口。在重新连接 USB 设备时，我可以使用`journalctl`命令来调试这个问题。

该命令用于查看`Systemd`日志，并且应该让我们知道当我们的 USB 设备插入时发生了什么。使用`-f`标志允许我们实时读取最近的日志。由此，我发现`pulseaudio`驱动程序会在设备一插上电源就声称设备无效，所以我们不能使用它！

修复非常简单，`gousb`库在`Device`类型上提供了一个名为`SetAutoDetach`的方法，它将把设备从`pulseaudio`上拿走。

> SetAutoDetach 启用/禁用自动内核驱动程序分离。启用自动分离时，gousb 将自动分离接口上的内核驱动程序，并在释放接口时重新连接。默认情况下，在新打开的设备句柄上禁用自动内核驱动程序分离。

```
const (
  product = 0x0078
  vendor = 0x09e8
)

func example() {
  ctx := gousb.NewContext()
  devices, _ := ctx.OpenDevices(findMPD26(product, vendor))

  // Detach the device from whichever process already
  // has it.
  devices[0].SetAutoDetach(true)
}

func findMPD26(product, vendor uint16) func(desc *gousb.DeviceDesc) bool {
  return func(desc *gousb.DeviceDesc) bool {
    return desc.Product == gousb.ID(product) && desc.Vendor == gousb.ID(vendor)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我面临的下一个问题是在`ActiveConfigNum`和`DefaultInterface`方法中。USB 设备使用的配置不允许我使用这些方法。这意味着我们必须自己决定使用哪种配置和接口。

为了解决这个问题，我决定手动遍历配置，然后遍历可用接口。一旦我们有了可以使用的接口，我们就找到了可以读取的`IN`端点。

这段代码有点难看，为了简洁起见，我排除了错误处理代码。我确信有更好的方法，但是为了学习，它达到了它的目的

```
// Iterate through configurations
for num := range devices[0].Desc.Configs {
  config, _ := devices[0].Config(num)

  // In a scenario where we have an error, we can continue
  // to the next config. Same is true for interfaces and
  // endpoints.
  defer config.Close()

  // Iterate through available interfaces for this configuration
  for _, desc := range config.Desc.Interfaces {
    intf, _ := config.Interface(desc.Number, 0)

    // Iterate through endpoints available for this interface.
    for _, endpointDesc := range intf.Setting.Endpoints {
      // We only want to read, so we're looking for IN endpoints.
      if endpointDesc.Direction == gousb.EndpointDirectionIn {
        endpoint, _ := intf.InEndpoint(endpointDesc.Number)

        // When we get here, we have an endpoint where we can
        // read data from the USB device
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了将所有这些连接在一起，我们需要一个类型来保存我们正在交互的 USB 设备的所有上下文信息。这就是名副其实的`MPD26`类型:

```
type MPD26 struct {
  // Fields for interacting with the USB connection
  context *gousb.Context
  device *gousb.Device
  intf *gousb.Interface
  endpoint *gousb.InEndpoint

  // Additional fields we'll get to later
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在需要的是一种不断从端点读取并将值写入通道的方法。我创建了一个名为`read`的未导出方法，一旦成功连接到 USB 设备，它就会在自己的 goroutine 中运行一个无限循环。为了清楚起见，错误处理再次被编辑。

```
func (mpd *MPD26) read(interval time.Duration, maxSize int) {
  ticker := time.NewTicker(interval)
  defer ticker.Stop()

  for {
    select {
    case <-ticker.C:
      buff := make([]byte, maxSize)
      n, _ := mpd.endpoint.Read(buff)

      data := buff[:n]
      // Do something with this data
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到这个方法有两个参数，`interval`和`maxSize`。`interval`参数决定了我们从 USB 设备读取数据的频率。需要注意的是，如果没有要读取的数据，调用`mpd.endpoint.Read`方法会停止进一步的执行，所以使用这个时间间隔只是确保我们不会太频繁地从设备读取数据。`maxSize`参数决定了我们在读取数据时应该使用的最大缓冲区大小。这两个值都可以从我们之前看到的设备配置中获得:

```
mpd := &MPD26{
  context: ctx,
  device: devices[0],
  intf: intf,
  endpoint: endpoint,
}

// The endpoint description defines the poll interval and max packet
// size.
go mpd.read(endpointDesc.PollInterval, endpointDesc.MaxPacketSize) 
```

Enter fullscreen mode Exit fullscreen mode

首先，让我们将字节数组的内容打印到`stdout`，这样我们就可以看到基于我们使用的控件的值的差异。下面是一些例子:

```
[11, 176, 1, 127] # Output when moving the first fader
[11, 176, 11, 127] # Output when moving the first knob
[9, 144, 36, 127] # Output when triggering a pad
[8, 144, 26, 0] # Output when releasing a pad 
```

Enter fullscreen mode Exit fullscreen mode

### 逆向工程串行数据

我们将使用读取原始 USB 数据得到的输出，对哪些值意味着什么做出一些假设。幸运的是，我们得到的值是 MIDI。因此，0-127 之间的任何方差通常都是您正在查看的控件值的良好候选值。根据控制台输出，数组中的最后一个字节似乎总是控件的 MIDI 值。

这意味着前 3 个字节应该表示我们正在使用的控件。我还没有弄清楚数组中的所有字节代表什么，但是某些控件有一致的值，所以我们可以用它们来更新库中控件各自的状态。

#### 推子&旋钮

推子和旋钮是最容易操作的控制。它们只有一个标识它们的数字和一个 0 到 127 之间的值。全部打完，前两个字节一致`[11, 176]`。我们可以使用这个信息来创建一个方法来识别一个消息是否是针对一个控件的值:

```
func isControl(data []byte) bool {
  // Knobs and faders all share the same two bytes in common, first and second
  // are always 11 and 176
  return data[0] == 11 && data[1] == 176
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单。下一个挑战是确定我们处理的是旋钮还是推子的变化。这可以使用数组中的第三个字节来确定，该字节包含推子的值从 1 到 6，旋钮的值从 11 到 16。使用这些，我们可以创建两个新的 helper 方法来识别我们正在获取消息的控件类型:

```
func isFader(data []byte) bool {
  // A fader is a control where the value of the third byte is always
  // 1 to 6
  return isControl(data) && data[2] >= 1 && data[2] <= 6
}

func isKnob(data []byte) bool {
  // A knob is a control where the value of the third byte is always
  // 11 to 16
  return isControl(data) && data[2] >= 11 && data[2] <= 16
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 护垫

垫有更多的逻辑，但工作方式相同。第一个字节确定是否按下或释放了键盘，第二个字节始终是 144，第三个字节是 26 到 51 之间的一个数字，用于标识被按下/释放的唯一键盘。下面是我们的方法:

```
func isPad(data []byte) bool {
 return (data[0] == 9 || data[0] == 8) && data[1] == 144 && (data[2] >= 36 && data[2] <= 51)
} 
```

Enter fullscreen mode Exit fullscreen mode

### 创建 Golang API

现在，我们需要以一种良好的方式公开这些数据，以便人们可以使用 MPD26 在 Go 中构建东西。前面我们看到了读取串行数据的代码，但我们需要一种方法，以一种对直接查看采样器的人有意义的格式获取数据。我们还希望事情异步工作，等待从 pad 读取不应该阻止从 fader 读取。

对于异步输出，我们将使用通道，我在`MPD26`类型中添加了以下字段:

```
// Channels for various components
faders map[int]chan int
knobs map[int]chan int
pads map[int]chan int 
```

Enter fullscreen mode Exit fullscreen mode

我还更新了`read`方法来调用`paseMessage`函数，该函数对输入类型进行分类并写入正确的通道:

```
func (mpd *MPD26) parseMessage(msg []byte) {
 defer mpd.waitGroup.Done()

 // Discard invalid messages.
 if len(msg) < 4 {
  return
 }

 mpd.waitGroup.Add(1)

 if isFader(msg) {
  go mpd.handleFader(msg)
  return
 }

 if isKnob(msg) {
  go mpd.handleKnob(msg)
  return
 }

 if isPad(msg) {
  go mpd.handlePad(msg)
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们现在有 3 个函数来处理每种输入`handlePad`、`handleKnob`和`handleFader` :

```
func (mpd *MPD26) handlePad(data []byte) {
 defer mpd.waitGroup.Done()

 num := int(data[2]) - 35
 val := int(data[3])

 channel, ok := mpd.pads[num]

 if !ok {
  return
 }

 channel <- val
}

func (mpd *MPD26) handleKnob(data []byte) {
 defer mpd.waitGroup.Done()

 var num int
 val := int(data[3])

 switch data[2] {
 case 12:
  num = 6
 case 11:
  num = 5
 case 14:
  num = 4
 case 13:
  num = 3
 case 16:
  num = 2
 case 15:
  num = 1
 default:
  return
 }

 // Check if there's a channel already listening
 // to this knob, if so, write to it. Otherwise
 // ignore the message.
 channel, ok := mpd.knobs[num]

 if !ok {
  return
 }

 channel <- val
}

func (mpd *MPD26) handleFader(data []byte) {
 defer mpd.waitGroup.Done()

 num := int(data[2])
 val := int(data[3])

 // Check if there's a channel already listening
 // to this fader, if so, write to it. Otherwise
 // ignore the message.
 channel, ok := mpd.faders[num]

 if !ok {
  return
 }

 channel <- val
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们只需要在`MPD26`类型上导出一些函数，人们可以使用这些函数来获得他们想要读取的面板/音量控制器/旋钮:

```
func (mpd *MPD26) Fader(id int) <-chan int {
    channel, ok := mpd.faders[id]

    if !ok {
        channel = make(chan int)
        mpd.faders[id] = channel
    }

    return channel
}

func (mpd *MPD26) Pad(id int) <-chan int {
    channel, ok := mpd.pads[id]

    if !ok {
        channel = make(chan int)
        mpd.pads[id] = channel
    }

    return channel
}

func (mpd *MPD26) Knob(id int) <-chan int {
    channel, ok := mpd.knobs[id]

    if !ok {
        channel = make(chan int)
        mpd.knobs[id] = channel
    }

    return channel
} 
```

Enter fullscreen mode Exit fullscreen mode

有了所有这些，我们现在可以连接和读取采样器。在未来，我想把它连接到一个音频库，比如 [beep](https://github.com/faiface/beep) 来获得一些实际的输出。但是现在，我们已经有了采样器的工作界面！

### 链接

*   [https://github.com/google/gousb](https://github.com/google/gousb)
*   [http://www.akaipro.com/products/legacy/mpd-26](http://www.akaipro.com/products/legacy/mpd-26)
*   [https://electronics . stack exchange . com/questions/80815/what-a-product-id-in-USB and-do-I-need-to-buy-it-for-my-project](https://electronics.stackexchange.com/questions/80815/what-is-a-product-id-in-usb-and-do-i-need-to-buy-it-for-my-project)
*   [https://godoc.org/github.com/google/gousb](https://godoc.org/github.com/google/gousb)
*   [https://github.com/faiface/beep](https://github.com/faiface/beep)