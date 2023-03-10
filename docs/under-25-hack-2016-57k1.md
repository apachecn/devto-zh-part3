# 25 岁以下黑客，2016

> 原文：<https://dev.to/laneone/under-25-hack-2016-57k1>

Under 25 Hack 是 2016 年 1 月的前几周在班加罗尔的 NUMA 安排的一次黑客活动。这是一次招聘黑客马拉松，也是我们第一次参加。

在我们用来保存移动数据的应用程序之后，我们看到了能够在超本地存储中移动文件的巨大企业潜力，我们改进了以前的产品，并将其命名为 BroGet，这是一个强大的文件共享系统。

BroGet 背后的直觉是，有些文件必须根据用户的地理位置推送给用户，我们使用混合存储来推这些文件，我们的演示针对三个产品使用案例，一个图书馆管理系统、一个办公文档管理系统和一个餐厅订单管理系统。理想情况下，我们会为家庭用户提供另一个用例，但它不会面向企业，该版本将免费供用户下载，并无限制地使用，从而促进用户在他们的手机上安装应用程序。

在图书馆的情况下，您可以想象一个插入图书馆局域网的基于 Raspberry Pi 的下载盒，它可以方便地将包含图书馆中所有现有书籍目录的电子书直接推送到用户的手机上，手机上运行的 BroGet 后台应用程序准备好接收电子书，并为图书馆提供通用接口，并提供从同一混合本地下载盒下载图书馆中已借出且当前不可用的书籍的电子书变体的能力。

在办公室的情况下，你可以想象一个重要的文件，该文件必须分发给每一个加入办公室的新员工，该文件可以一次上传到连接到办公室局域网连接的混合本地下载箱，并且当他/她加入公司网络时，它可以方便地将所述文件发送给每一个用户。这将允许用户在进入办公室的地理位置时接收文档。广播消息和所有公共公告可以通过 BroGet 分发，因为该应用程序会根据消息的优先级自动同步和更新最相关的文档，供用户查看。

在餐馆订单管理系统的情况下，我们最好在餐馆附近设置一个基于 Raspberry Pi 的下载框，并将菜单作为电子书上传到设备上。菜单/定价和计费系统将共存于同一堆栈中，并将保持离线，当用户输入餐馆的地理位置时，菜单将从下载框推送到用户手机上的应用程序，后台应用程序将接收文件，并提供通用接口以从应用程序订购食物。在这种情况下，BroGet 成为餐馆订单管理下载框的超级本地终端。用户现在可以从他的手机上订购他的菜单，订单将直接发送到厨房，在那里一个哑终端从下载箱接收订单，并向厨师显示厨房准备的菜单项列表。后端允许订单通过云输入，以确保送货上门也被跟踪。

在家庭使用案例中，它很像允许你保存移动数据的应用程序，只是这次你购买了 BroGet 下载盒，当你不在家时，你可以让它为你下载东西。

整个套件是面向企业的，我们做了演示来展示不同的使用案例，评委们对允许他们使用移动数据批准下载的演示印象深刻。一位评委认为，一个好的观点是，我们的团队缺乏演讲技巧来展示我们产品线的全部用途。一家初创公司找到了我们，他们希望与我们合作，因为他们正在开发一个具有混合云存储系统的系统，而 BroGet 与他们相关。

作为一个招聘黑客马拉松，第一名是在一家初创公司的工作，你可以从促成这次黑客活动的赞助商名单中选择。我们获得了第二名，并获得了一个名为 Teevee 的印度镀铬衍生产品和一个树莓 Pi 2，外加见证新德里印度创业大会的门票。