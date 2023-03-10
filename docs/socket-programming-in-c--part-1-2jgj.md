# C 语言中的套接字编程:第 1 部分

> 原文：<https://dev.to/abhishekalbert/socket-programming-in-c--part-1-2jgj>

你好，很高兴见到你！！！

今天在这篇文章中，我将谈论 C 语言的套接字编程。

在开始讨论我们的主题之前，我们首先要了解学习套接字编程的先决条件。

*   对 C 语言的基本理解。
*   linux 命令行的基本知识，在 Linux 系统上编辑文本文件(如果你在基于 Linux 的操作系统上工作)。
*   应该知道 HTML，CSS 和 Javascript 的基础知识。
*   网络术语的基础，如数据包、协议、协议组(TCP/UDP)。(*我们稍后将讨论网络的基础知识*)。

如果您不熟悉这些，请不要惊慌，这里有一些好的资源列表，您可以从中了解开始这个主题或继续这篇文章所需的所有知识。但是如果你知道这很好，那么你可以直接从“什么是套接字？?"。

所以这里列出:

1.  [C 编程入门从零开始](https://handmadehero.org/watch)或[初学者 C 编程教程](https://www.youtube.com/watch?v=KJgsSFOSQv0)。
2.  [面向 Web 开发人员的 HTML、CSS 和 Javascript】](https://www.coursera.org/learn/html-css-javascript-for-web-developers)
3.  [初级网络课程](http://www.steves-internet-guide.com/basic-networking-course/)或[初级网络入门](https://www.udemy.com/introduction-to-networking-for-complete-beginners/)

# 网络术语基础

* * *

### 数据包:

* * *

A packet is the unit of data that is routed between an origin and a destination on the Internet or any other packet-switched network.
OR
A packet is a container or box that carries data over a TCP/IP network and internetworks.
*Explanation* : Whenever a node on a network sends some data over the network, it passes the data frame to the switch, and later to the router. The router, after looking at the destination IP addresses, encapsulates the data and routes it toward the recipient. This encapsulated data is the packet that is forwarded over the network.
Packets contain two distinct types of information to reach the destination completely and correctly, namely control information and the data it is carrying. The control information includes source destination addresses, sequencing format, error detection and correction mechanisms, all of which help to ensure the optimal delivery of data. The control information usually resides in the header and trailer, encapsulating the user data in between them.

### 协议:

* * *

Network protocols are formal standards and policies comprised of rules, procedures and formats that define communication between two or more devices over a network. Network protocols govern the end-to-end processes of timely, secure and managed data or network communication.
*Explanation :* Network protocols incorporate all the processes, requirements and constraints of initiating and accomplishing communication between computers, servers, routers and other network-enabled devices. Network protocols must be confirmed and installed by the sender and receiver to ensure network/data communication and apply to software and hardware nodes that communicate on a network. There are several broad types of networking protocols, including: Network communication protocols: Basic data communication protocols, such as TCP/IP and HTTP. Network security protocols: Implement security over network communications and include HTTPS, SSL and SFTP. Network management protocols: Provide network governance and maintenance and include SNMP and ICMP.

### 网络协议:

* * *

Internet Protocol suite (IP suite) is the standard network model and communication protocol stack used on the Internet and on most other computer networks. While other networking models exist, the IP suite is overwhelmingly the global standard for computer-to-computer communication. *Explanation :* The IP suite follows a client/server model, in which multiple client programs share the services of a common server program. Protocols in the suite define end-to-end data handling methods for everything from packetizing, addressing and routing to receiving. Broken down into layers the IPS includes the link layer, the internet layer, the transport layer, application layer and the physical layer. Each layer contains a number of protocols for communications. The suite is sometimes just called TCP/IP, because those are the predominant protocols in the model and were the first ones used. However, the IP suite involves many other protocols. The following is a partial list of protocols in each layer:

#### 应用层:

BGP, DHCP, DNS, FTP, HTTP, IMAP, LDAP, MGCP, NNTP, NTP, POP, ONC/RPC, RTP, RTSP, RIP, SIP, SMTP, SNMP, SSH, Telnet, TLS/SSL and XMPP.

#### 传输层:

TCP, UDP, DCCP, SCTP and RSVP.

#### 互联网层:

IP, IPv4, IPv6, ICMP, ICMPv6, ECN, IGMP and IPsec.

#### 数据链路层:

ARP, NDP, OSPF, L2TP, PPP, MAC, Ethernet, DSL, ISDN and FDDI. For more detail on Protocols click [here](https://whatis.techtarget.com/definition/Internet-Protocol-suite-IP-suite). A video on protocols Visit [here](https://youtu.be/wAIT9q6-OT8).

#### TCP/UDP 简介:

please click [here](https://www.linkedin.com/learning/comptia-network-plus-n10-006-cert-prep-4-making-tcp-ip-work/introduction-to-tcp-and-udp).

# 多路分解:

将主机到主机的数据包传送服务转换为进程到进程的通信通道。
[![](img/a98469e711c69715886a28c9bcd678fd.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--NCqrMVg4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x9mjpr2jq79y7in4fqgi.png)

# 字节排序:

●两种“字节排序”
–网络字节顺序:数字的高位字节是
存储在内存中最低地址
–主机字节顺序:数字的低位字节是
存储在内存中最低地址
–网络栈(TCP/IP)期望网络字节顺序
●转换:
–htons()-主机到网络短
–htonl()-主机到网络长
–ntohs()-网络到主机短
–nto

我觉得对于这篇文章来说已经足够了。在下一部分，我们将讨论什么是插座和插座的类型等。

所以下一篇文章再见，希望能对你有所帮助。

祝你好运！！继续学习！！！还有快乐编码！！！！