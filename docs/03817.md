# 介绍 Radix 命令行界面(CLI) -内部黑客马拉松冠军！

> 原文：<https://dev.to/radixdlt/introducing-the-radix-command-line-interface-cli-internal-hackathon-winner-5cie>

# Radix 命令行界面(CLI)

Radix 的一个重点是确保开发者能够轻松构建[平台。因此，对于首次内部黑客马拉松项目，James 和我决定构建一个命令行界面(CLI ),用于与 Radix ledger 进行交互。](https://docs.radixdlt.com/alpha/learn/platform/buildability)

# 什么事？

我们构建了一个 CLI，允许用户执行简单的功能，如检查他们的帐户余额和进行交易。在设计方面，我们专注于创建一个遵循 [UNIX 工具哲学](https://www.tldp.org/LDP/GNU-Linux-Tools-Summary/html/c1089.htm)的工具:创建简单的输入/输出程序，然后使用 UNIX 管道(“|”)的魔力将多个程序“缝合”在一起。这保持了功能的紧凑性，并允许用户利用现有的众所周知的工具。网络世界中的对等物是微服务。

# 怎么样了？

经过几个小时的 Java 编程、James 的 DevOps 魔术和几杯朗姆酒，我们成功地创建了一个简单的工具，它可以在 Radix Ledger 上异步读写事务。

下面是一个简单的例子，展示了如何阅读发送到我的地址的邮件:

> $ radcli 获取消息
> { "from ":"
> 
> ” “to”: “” “msg”: “Whatsup!” }
> { “from”: “” “to”: “” “msg”: “Josh, where are you?!” }
> { “from”: “” “to”: “” “msg”: “Yo!” }

现在，如果我想在我的邮件中搜索“Josh ”,我可以使用 grep，这是一个普遍存在的命令行实用程序，用于搜索数据:

> $ rad CLI get messages | grep Josh
> { " from ":"
> 
> ” “to”: “” “msg”: “Josh, where are you?!” }

管道的力量！‍

# 账外智能 Contracts‍

作为黑客马拉松的最后一个例子，我们展示了一个简单的 CLI 和管道如何创建非常强大的帐外“智能合同”

这是一个自动支付链:

> $出口储蓄 _ 百分比=0.10
> $出口储蓄 _ 地址=
> 
> $ radcli get transfers | jq --unbuffered -r .amount | awk -v percent="$SAVINGS_PERCENTAGE" '{print "-c \"print " percent " * " $1 "\""; system("") }' | xargs -L1 python | awk -v tok=’radcli nativetoken’ -v address="$SAVINGS_ADDRESS" '{print "send
> tokens " $1 " " tok " to " address; system("") }' | xargs -L1 radcli

在上面的例子中，Sophie 希望将进入她日常钱包的每笔付款的 10%存起来，并创建一个自动支付链，指示将这 10%发送到她的储蓄钱包。当 Sophie 收到付款 10 时，1 (10%)会自动发送到她的储蓄钱包中，无需 Sophie 的进一步干预。

最后，这里有一个水龙头服务，它可以将 10 XRD 传输给任何向我的地址发送消息的人:

> $ rad CLI get messages | jq-un buffered-r . from | grep
> -line-buffered-v ' rad CLI myaddr ' | awk-v Tok = ' rad CLI native token ' ' { print " send token 10 " Tok " to " $ 1；系统(" " }' | xargs -L1 拉德 cli

通过使 CLI 保持简单并可与管道一起使用，无需任何额外工作就可以实现更复杂的功能，就像变魔术一样！

但最重要的是，我们都很高兴我们的工作被同事评为第一名！

*注意:由于 CLI 是为了与内部 betanet 交互而构建的，因此我们将仅在公共 betanet 上线后对其进行开源。*

有问题吗？在 Radix discord 组([https://discord.gg/CZ6WMfW](https://discord.gg/CZ6WMfW))给我或詹姆斯发消息，或者你也可以在我们的电报频道([https://t.me/radix_dlt](https://t.me/radix_dlt))问他们。

订阅我们的邮件列表，以便在我们达到重大里程碑时得到通知-[https://radixdlt.typeform.com/to/nyKvMV?source=DecentraSign](https://radixdlt.typeform.com/to/nyKvMV?source=DecentraSign)

原始博客帖子，包括演示文稿中的图片-[https://www . radix DLT . com/post/introducing-the-radix-command-line-interface-CLI-based-on-the-the-UNIX-tools-philosophy](https://www.radixdlt.com/post/introducing-the-radix-command-line-interface-cli-based-on-the-unix-tools-philosophy)

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询