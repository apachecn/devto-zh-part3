# Modbus 扫描仪。如何解析 Modbus 消息

> 原文：<https://dev.to/oscar37921395/modbus-scanner-how-to-parse-modbus-messages-l7p>

曾经中央工业系统使用 Brobdingnagian 橱柜的时代已经一去不复返了，橱柜中的仪器通过数公里厚的电缆连接到传感器和致动器。今天，在绝大多数情况下，放入许多本地控制器会更有利可图，这些本地控制器联合在一个非常单一的网络中，并通过 Modbus 等经济的通信协议向服务器发送信息。与集中式系统相比，这允许节省商业仪器的安装、测试和维护。

[在本文](https://www.virtual-serial-port.org/article/modbus-scanner/)中，我们将告诉您为什么 Modbus 通信协议是工业网络的高级选择，以及先进的 COM 端口程序计算机代码可以方便您以最简单和最方便的方式扫描 Modbus 消息。