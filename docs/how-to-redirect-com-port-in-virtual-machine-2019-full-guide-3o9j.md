# 如何在虚拟机中重定向 COM 端口- 2019 完整指南

> 原文：<https://dev.to/oscar37921395/how-to-redirect-com-port-in-virtual-machine-2019-full-guide-3o9j>

虚拟化为我们在同一台电脑上使用多个操作系统环境提供了一种简单高效的方式。通过在您当前的操作系统上运行虚拟机，您可以灵活地测试不同的操作系统、访问为其设计的软件系统或应用程序、制作操作系统备份等。方便的是，您只需要一组物理资源，如 CPU、RAM 和磁盘驱动器空间。

[![Virtual machine serial port](img/4e53877daf7fa51a410cf8c93787ed9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wTnvyRKk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.serial-server.net/imgnew/products/sec/splash/img4%402x.png)

如果你有一个串行打印机，调制解调器或任何其他基于 com 的外围设备连接到你的本地计算机，并需要把这个设备连接到你的虚拟机，你将能够通过一个虚拟的 COM 端口。想知道一个改变虚拟机中 COM 口的方法？这完全取决于您使用的虚拟化软件系统。一些虚拟化应用允许您在创建一些额外配置时连接到主机的串行设备，而替代解决方案不提供对主机 pc 的物理串行端口的访问，除非您使用通过网络运行的专用第三方工具。

[在这篇文章](https://www.serial-server.net/com-port-in-virtual-machine.html)中，我将描述在虚拟机中改变 COM 端口重定向的几种常见策略。