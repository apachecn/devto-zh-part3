# 无需解析即可完美构建日志

> 原文：<https://dev.to/gdcohen/perfectly-structuring-logs-without-parsing-2f92>

开发人员和测试人员经常使用日志文件和度量标准来查找和排除故障。但不幸的是，获取有用的内容通常涉及数据争论、正则表达式和解析脚本。

下面是一个例子，使用我的 Mac 上的/var/log/system.log 中的一个典型日志行:

```
May  9 05:35:12 Gavins-MacBook-Pro com.apple.xpc.launchd[1] (com.apple.sandboxd[7344]): Service exited due to SIGALRM | sent by kernel_task[0] 
```

现在，假设您想要构建一个报告，显示哪些服务退出了，以及退出的原因。比如这样的:

```
REASON          SERVICE
SIGALRM         com.apple.sandboxd
SIGKILL         QA2G25RMZ4.com.wunderkinder.wunderlist-helper
SIGKILL         com.apple.AirPlayUIAgent
SIGKILL         com.apple.LocalAuthentication.UIAgent
SIGKILL         com.apple.OSDUIHelper
. . .            . . .
. . .            . . . 
```

首先，您需要理解如上所示的日志行的结构，这样您就可以获得报告所需的数据。通过一点观察(和一些猜测)，您可能会想到(下划线的大写字母代表参数):

*月日 HH:MM:SS 主机进程 1 [PID1](https://dev.toSERVICE%5BPID2%5D) :服务因进程 2[PID3]发送的信号类型|而退出*

您还需要能够唯一地识别这个“事件类型”的所有出现，以便有效地解析出数据。这可以通过搜索一些识别文本来完成，例如“服务已退出”。我用 grep 试了一下，结果发现它太笼统了，因为它也匹配其他类似的事件，比如这个:

```
May  8 13:39:49 Gavins-MacBook-Pro com.apple.xpc.launchd[1] (com.apple.WebKit.Networking.A4992C6F-8F69-4EA9-A031-76B032FB964F[7720]): Service exited with abnormal code: 1 
```

将搜索词改为“服务退出原因”似乎可以解决问题，但是我只在一个小的日志文件中测试了这一点，所以可能有其他事件类型也匹配这个词。这里重要的一点是，您需要一种唯一标识事件类型的方法，否则您的解析将生成错误的数据。

接下来，您需要编写一个程序或脚本来查找相关的日志行，剔除原因和服务，并输出结果。为了使报告更具可读性，您还需要过滤掉重复的行(我的 grep 显示了许多行),按退出原因排序，并按服务进行子排序。你可以这样做:

找到所有匹配的事件
通过使用正则表达式并解析出正确的数据来提取原因和服务
按原因对结果进行排序
对于每个原因，按服务进行子排序
打印出包含原因和服务的行，并取消任何具有相同原因和服务的后续行

尽管上述内容并不涉及火箭科学，但它确实需要找出正确的正则表达式和解析表达式，以及适当的编码/脚本技能。但这还不是全部。

如果事件来自您自己的应用程序，您还需要跨软件版本维护它。将文本或参数添加到现有的事件类型中，或者添加新的类似事件是很常见的，这两种情况都会破坏您的解析。更大的问题是，您可能永远也不会发现您的脚本停止了寻找预期的匹配。

然后是敲弯器。上面的讨论只涉及一种事件类型。大多数应用程序有数千种不同的事件类型，每一种都有自己的结构。尽管您可能不需要挖掘每个事件类型，但是您需要挖掘的时间通常是最不方便的(例如，您的 bug 可能会阻碍一个关键的构建，或者您可能会被拖进客户升级)。

我们想出了一个新方法

Zebrium 使用无辅助机器学习来:
识别所有独特的事件类型(通常每个应用程序有数千个)
为事件类型构建一个模式(结构化固定文本和可变参数)
将每个事件类型放入一个视图中，其中每个参数都有自己的类型化列

所有这些都是为简单查询而构建的，并且随着日志文件结构的变化而自我维护。

**结果**

我将上面提到的日志文件(/var/log/system.log)拖到了 Zebrium UI 中。仅仅使用这个文件，没有预先学习或数据争论，我们的机器学习就展示了它的魔力:

它识别了 500 多种独特的事件类型，并为每种事件类型创建了一个视图
它建立了一个索引，并按主题对每种事件类型进行了分类，这使得找到我要找的东西变得非常容易(它被命名为“v _ service _ exited _ due _ sent _ by”)
然后一行 SQL 语句生成了报告(实际上只有一行，但我将它分成了三行，以使它在).下面更易读)注意，您也可以通过我们的 UI 获得这些数据，而无需编写任何 SQL。

```
SELECT DISTINCT _to AS Reason, _str AS Service 
FROM v_service_exited_due_sent_by 
ORDER BY Reason, Service; 

Reason   |                             Service                              
---------+------------------------------------------------------------------
 SIGALRM | com.apple.sandboxd
 SIGKILL | QA2G25RMZ4.com.wunderkinder.wunderlist-helper
 SIGKILL | com.apple.AirPlayUIAgent
 SIGKILL | com.apple.LocalAuthentication.UIAgent
 SIGKILL | com.apple.OSDUIHelper
 SIGKILL | com.apple.SystemUIServer.agent
 SIGKILL | com.apple.UserEventAgent-Aqua
 SIGKILL | com.apple.ViewBridgeAuxiliary 
```

一点也没花时间。我不必考虑正则表达式、解析、编写代码等等。我不需要提前准备任何东西，也不需要提前知道我想查询什么。在没有预先学习和很小的数据集(58K 行)的情况下，我们的 ML 构建了整个日志文件。只用一行 SQL 语句，我就生成了我想要的报告！

我打算在这里结束博客，但决定做一件事！我想看看是否有任何由于信号而退出的服务也以异常退出代码退出(还记得上面提到的第二个事件“服务以异常代码退出”)。嗯，你猜对了，这也可以用一行 SQL 来完成。我所要做的就是连接两种事件类型的视图。下面是结果:

```
SELECT DISTINCT _to AS Reason, a._str AS Service, _code 
FROM v_service_exited_due_sent_by A 
  JOIN (SELECT DISTINCT _str, _code 
    FROM v_service_exited_abnormal_code
  ) B ON A._str = B._str; 

 Reason  |            Service            | _code 
---------+-------------------------------+----------
 SIGKILL | com.apple.ViewBridgeAuxiliary |        2 
```

如果你是处理获取日志文件信息的开发人员或测试人员，请在这里预先注册我们的测试程序[。](https://www.zebrium.com/preregister-for-beta)