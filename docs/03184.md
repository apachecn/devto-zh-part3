# 从虚拟机访问串行端口

> 原文：<https://dev.to/oscar37921395/accessing-serial-ports-from-a-virtual-machine-2nao>

[![](img/41e9242db495f0ce1831dfb1f27b77da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UwTqNBnC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rwxsn1xbw6xaa0s0h0qr.jpg) 
**从虚拟机访问串口的简单方法**

通过使用称为[串行到以太网连接器](https://www.serial-server.net)的专用通信软件应用程序，可以将虚拟机连接到远程串行端口和设备。它通过创建完全模拟物理串行接口的虚拟串行端口来工作。使用这个强大的软件工具，您可以创建从客户操作系统到任何联网计算机上的物理 COM 端口的连接。连接到服务器物理端口的设备可通过客户机上创建的虚拟端口在网络上使用。
为了创建虚拟 COM 端口以共享您的串行设备，请下载串行至以太网连接器应用程序，并在提供共享端口的主机上安装该程序。它还需要安装在将访问端口和任何连接设备的客户机上。
该软件支持许多虚拟环境，如 VMware、Citrix XenDesktop 和 Hyper-V，使您能够使用这些平台来使用虚拟串行端口，就像它们是实际的物理接口一样。

文章:[https://www . serial-server . net/com-port-in-virtual-machine . html](https://www.serial-server.net/com-port-in-virtual-machine.html)