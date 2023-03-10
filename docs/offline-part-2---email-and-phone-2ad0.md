# 离线第 2 部分-电子邮件和电话

> 原文：<https://dev.to/xet7/offline-part-2---email-and-phone-2ad0>

本系列之前的介绍文章:[线下是新常态](https://dev.to/xet7/offline-is-the-new-normal-24b4)。

## 邮件步骤从 1 到 3

1.  在 1991-1994 年间，我确实运营过 BBS，使用[https://en . Wikipedia . org/wiki/QWK _(file _ format)](https://en.wikipedia.org/wiki/QWK_(file_format))上传和下载最新消息。
2.  之后，我使用了一些电子邮件客户端，比如雷鸟，它用 POP 下载电子邮件，或者用 IMAP 保存在服务器上。几年前，我们的 Linux 服务器托管在芬兰。
3.  之后，在工作中，我用 YippieMove 服务将邮件转移到 Google Apps / G 套件，这是一个专门移动大量邮件并重试失败传输的快速服务器群。有些人确实也使用雷鸟移动他们的电子邮件。我已经不在那里工作了，但仍然管理着我自己和我的客户的一些 G Suite 账户。

## 回到上面的第 2 步:雷鸟

1.  我确实从我的硬盘上找到了一些旧的雷鸟备份，所以它的目录是/home/username/。Linux 上的雷鸟。
2.  在 Gmail 中，我将所有过滤器保存到文件中，并将标签一页页复制粘贴到文本文件中。
3.  在 Gmail 设置中，我对所有邮件都启用了 POP，这样当邮件用 POP 下载到雷鸟后，它就会从 Gmail 服务器上删除邮件。
4.  在 Gmail 帐户设置中，我为雷鸟创建了应用程序专用密码。
5.  在 Thunderbird 中，我确实用 POP 设置了 Gmail 帐户，并使用了上面第 4 步中特定于应用程序的密码。在雷鸟上，我点击了“获取信息”按钮，下载了大约 500 条信息。在那之后，我需要再次点击“获取信息”，所以它下载了大约 500 条信息。如此反复，直到我所有的邮件都下载到我的收件箱。是的，这包括发送和接收的邮件，都在收件箱中。
6.  现在我已经收到了所有的电子邮件。雷鸟目录。当我用命令“du -sh”检查它的大小时。雷鸟“它有 2 GB 的文件，可能有大约 50k 封电子邮件，尽管我还不知道如何进行精确的计算。

## 可能:Exchange、Office 365 Online、Outlook

1.  几年前，我确实让雷鸟和 Exchange 一起工作，使用 DavMail 收发电子邮件，使用雷鸟插件处理 Exchange 日历。雷鸟<=> DavMail <=> Exchange 使雷鸟认为它正在连接到标准的 SMTP/IMAP 服务器，而实际上 DavMail 连接到 Exchange，将所有请求转换为正确的格式。我确实更新了 https://github.com/wekan/wekan/wiki/Troubleshooting-Mail[的网页](https://github.com/wekan/wekan/wiki/Troubleshooting-Mail)以获取最新信息，并且尝试了来自[的一些提示，但是还没有成功。这可能取决于所使用的 Exchange 版本。](https://stackoverflow.com/questions/6801174/davmail-office365)
2.  另一种可能是在 Windows VM 上使用 Outlook 下载电子邮件，然后将电子邮件转移到其他 IMAP 服务器，然后从该服务器下载 POP 到 Thunderbird。
3.  如果一些工作场所仍然使用 Exchange，那么通过 DavMail 可以设置朋友[https://github.com/wekan/wekan/wekan/wiki/Friend](https://github.com/wekan/wekan/wekan/wiki/Friend)的网络邮件来使用 Exchange。

## 电子邮件可能的后续步骤

*   添加其他电子邮件帐户以使用 POP 下载邮件
*   创建文件夹，并使用 Thunderbird 插件自动分类电子邮件到不同的文件夹。
*   使用 Enigmail 雷鸟插件发送和接收 PGP 加密的电子邮件。
*   使用 Qubes 附件插件向不同的虚拟机发送附件。是的，我终于开始运行 Qubes OS 4 了，在得到新的规格足以满足 Qubes 4 硬件要求的笔记本电脑之后。我在它自己的虚拟机上运行电子邮件。我的大多数 Qubes 虚拟机都禁用了互联网接入。只有网络浏览器虚拟机和电子邮件虚拟机可以访问互联网。
*   复制电子邮件。雷鸟目录到我的 RasPi3，用雷鸟在 RasPi3 上阅读和发送电子邮件。
*   下载 Gmail 联系人并将其添加到雷鸟联系人中。
*   下载 Gmail 日历并将其添加到雷鸟日历。
*   制作许多备份到不同的硬盘驱动器，DVD 等媒体。现在，保证我的数据安全、可靠、可用等不是谷歌的责任。我现在只能怪自己。
*   继续从网上下载更多我的数据，并从网上删除。
*   更改我的域名以使用其他电子邮件提供商，如[https://protonmail.com](https://protonmail.com)
*   如果在某个芬兰服务器提供商处运行我自己的电子邮件服务器，也许它可以工作一段时间，如果我正确设置了 SPF/DKIM/DMARC 电子邮件设置，有 25 个端口开放用于接收电子邮件，并且如果配置正确，服务器不会发送太多的服务器通知电子邮件。但是，如果电子邮件服务器出现在垃圾邮件黑名单中，我可以付费使用丹麦的电子邮件发送服务[https://mysmtp.com](https://mysmtp.com)来确保 Gmail 和其他具有正确 DNS 域名服务器 SPF/DKIM/DMARC 电子邮件设置的电子邮件服务的可送达性。电子邮件接收仍然是我的服务器端口 25。
*   在芬兰，一些电缆调制解调器互联网提供商可以为 web 服务器开放端口 80 和 443，但没有一个提供商开放电子邮件接收端口 25 来接收来自互联网的电子邮件。因此，需要一些变通方法来接收到自托管沙暴端口 25 的电子邮件，该端口将接收到自托管[https://Sandstorm . io](https://sandstorm.io)round cube grain 的电子邮件。
*   设置好友[https://github.com/wekan/wekan/wekan/wiki/Friend](https://github.com/wekan/wekan/wekan/wiki/Friend)并使用好友访问所有应用。

## 我用安卓智能手机做了什么

1.  我把我的 SIM 卡从我的 Android 智能手机转移到我的没有摄像头和互联网的 dumbphone 上。Dumbphone 只有电话和短信，以及一些在 ohine 上本地工作的应用程序。
2.  在 Android 手机设置中，我启用了无线局域网。似乎我使用的所有 Android 应用程序都可以很好地支持无线局域网接入。
3.  在 Android 手机设置中，我关闭了大多数应用程序的权限，包括谷歌自己的应用程序。所以我禁用了位置、摄像头、麦克风、文件存储等所有可能的权限。我还取消了一些我订阅的付费服务。
4.  我确实用安卓应用转了一些钱到我的预付卡里。所以在外面时，我的预付卡上只有一些现金和有限的钱。我还从钱包里取出了所有其他的支付卡、收据等。然后我把安卓智能手机关了。

## dumbphone 是什么感觉

1.  去市中心的时候，我只带了我的小耳机和钱包，里面的东西很少。我没有带安卓智能手机。它确实觉得我不再那么匆忙了。我不像以前那样经常看我的智能手机。这是一种解脱。我确实走得更慢了，享受了更多，看到了更多发生在我周围的事情。我梦想着下一步我会用我的没有联网的新旧电脑做些什么。当我回到家时，我对我的妻子说，我感觉我的生活又回来了。
2.  我开始设置我的 Amiga 1200，这样我就可以打开它，但我确实感觉有些不对劲。我有时需要打开一个 1200 的箱子，检查里面是否一切正常，比如没有灰尘，电池是否正常等等。我有点担心它会发生什么事情，就像我的 Amiga 500 一样，当我长时间打开电源后，确实有烟冒出来，所以目前我的朋友正在看我的 Amiga 500 有什么问题。
3.  我确实认为这种“以旧换新”和对这种技术的恐惧是有关系的。我以前没有注意到这种恐惧，因为我只是在最近几年才习惯于主要用计算机软件工作，并在网络空间花了太多时间。我开始阅读《超越恐惧:在不确定的世界中理智地思考安全》一书。布鲁斯·施奈尔写的，这样我就能明白这种恐惧是怎么回事了。

未完待续…

(最初发布于:[https://blog . wekan . team/2019/01/offline-part-2-email-and-phone/index . html](https://blog.wekan.team/2019/01/offline-part-2-email-and-phone/index.html))