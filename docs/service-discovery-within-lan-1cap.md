# 局域网内的服务发现

> 原文：<https://dev.to/itzmeanjan/service-discovery-within-lan-1cap>

## 想法

让我们假设一种情况——你有两个设备在同一个*局域网*内，你想从*设备 A* 向*设备 B* 发送一个文件。

```
 deviceA -> deviceB 
```

> 那你会怎么做？
> 
> 为**设备 A** 编写一个服务器程序，为**设备 B** 编写一个客户端程序🤔。

这很有效。但是下次当您运行这些程序时， *deviceA* 和 *deviceB* 可能有不同的 IP 地址，或者另一个 *deviceC* 可能有兴趣参与文件传输，这可能最终导致您手动更新*对等方的* IP 地址😞。

> 我们创建一个自动设备发现服务怎么样？
> 
> 听起来不错👏

所以让我们开始工作吧🏃

## 型号

我们将使用 **DatagramSocket** ，用于处理局域网中的*服务广告*和*服务发现*。实现将使用 *Dart 语言*完成，并将提供 *JavaScript* 的片段。

## 初始化

首先我们要写*服务广告*也就是一个 UDP 服务器程序。

### 服务广告客户

实现建议功能所需的类。

```
import 'dart:io' show RawDatagramSocket, RawSocketEvent, InternetAddress, Datagram;
import 'dart:convert' show utf8; 
```

在 *InternetAddress.anyIPv4* 和端口 *8000* 上绑定一个 *RawDatagramSocket* ，使其可以监听端口 *8000* 上的所有接口。

```
RawDatagramSocket.bind(InternetAddress.anyIPv4, 8000).then((datagramSocket) {
    // more code coming ...
  }); 
```

可以看到 *RawDatagramSocket.bind(...)*返回一个*未来*。我们将在这个套接字上启用 *READ* 事件监听器，这样每当任何其他设备试图在这个套接字上写东西时，监听器都会得到通知。

```
datagramSocket.readEventsEnabled = true; // put this line within aforementioned `then()` 
```

现在我们将在这个
*DatagramSocket* 上监听 *READ* 事件。每当新的 *READ* 事件发生时，使用 *DatagramSocket.receive()* 获取该事件，并通知其他终端它已经到达有效的*服务广告者*。

```
datagramSocket.listen((RawSocketEvent event) {
      if (event == RawSocketEvent.read) {
          // checking whether it's a read event or not
        Datagram dg = datagramSocket.receive();
        if (dg != null) {
        // notifying other end that it has reached a service advertiser
          datagramSocket.send(dg.data, dg.address, dg.port);
          print('${dg.address}:${dg.port} -- ${utf8.decode(dg.data)}');
        }
      }
    }); 
```

*对等体的* IP 地址、端口号和接收数据从*数据报*对象中提取，在 *READ* 事件发生时接收。

使用来自 *utf8* 模块的以下函数，从*列表*将接收到的数据解码回*字符串*。

```
utf8.decode(dg.data); 
```

井*服务广告商*完成😉。

### 服务发现者

你可能已经猜到了，这只是一个 UDP 客户端。

实现服务发现功能所需的类。

```
import 'dart:io' show RawDatagramSocket, RawSocketEvent, InternetAddress, Datagram;
import 'dart:convert' show utf8; 
```

我们再次需要在 *InternetAddress.anyIPv4* 和端口 0 上绑定一个 *RawDatagramSocket* ，它将返回 *Future* 。

> 为什么是端口 0？🤔
> 
> 指定端口号 0，让我们使用操作系统选择的端口号。在客户端，我们对监听预定义的端口号不感兴趣😊。

```
RawDatagramSocket.bind(InternetAddress.anyIPv4, 0).then((datagramSocket) {
    // more code coming ...
  }); 
```

在*服务广告*期间，我们只是在 *RawDatagramSocket* 上监听 *READ* 事件，但现在我们也要启用*广播*，这样这个 Socket 就可以向 *255.255.255.255* 发送*广播消息*，该消息将到达局域网内的所有设备。

```
// put these two lines with in previous `then()`
datagramSocket.broadcastEnabled = true;
datagramSocket.readEventsEnabled = true; 
```

在*广播地址*和端口 8000 上发送消息的时间。

```
datagramSocket.send("io.github.itzmeanjan.transferZ".codeUnits,
        InternetAddress("255.255.255.255"), 8000); 
```

上述消息到达 LAN 中所有侦听端口 8000 上的 *READ* 事件的设备。

> 为什么是 8000 端口？🤔
> 
> 我们将端口 8000 用于*服务广告*😉。

你可能会好奇什么是*“io . github . itzmeanjan . transferz”。codeUnits* ，从*字符串*返回*列表*，作为*广播消息*发送。

现在让我们把监听*读*事件所需的逻辑放到 *RawDatagramSocket* 上。下面的代码片段非常接近我们在*服务广告*中使用的代码片段，除了我们不向*服务广告*发送任何东西。

```
datagramSocket.listen((RawSocketEvent event) {
      if (event == RawSocketEvent.read) {
        Datagram dg = datagramSocket.receive();
        if (dg != null) {
          print('${dg.address}:${dg.port} -- ${utf8.decode(dg.data)}');
          //datagramSocket.close();
          // You may consider uncommenting previous line, if you want to explore only one Service Advertiser at a time.
        }
      }
    }); 
```

我们的*服务发现者*完成了😃。

## 运行

是时候运行它了🏃

### 服务广告客户