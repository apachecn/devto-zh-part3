# Radix Java 客户端库-入门

> 原文：<https://dev.to/radixdlt/the-radix-java-client-library-getting-started-5426>

# 简介

在本指南中，我们将使用 [Java 客户端库](https://github.com/radixdlt/radixdlt-java)从头开始构建一个小型分布式应用程序(d App)。你将在本教程中学习的技术是在[基数](http://www.radixdlt.com/)上制作任何 DApp 的基础，掌握它将让你更好地理解基数分布式分类帐。

# 关于我们的例子 DApp

作为本指南的示例 DApp，我们将构建一个简单的聊天机器人，接收和回复发送到特定基数地址的消息。通过我们的小型聊天机器人 DApp，您将学会:

-引导并连接到基数分布式分类帐
-创建基数标识
-从该标识创建基数地址
-向其他基数地址发送和接收加密消息

如果您不熟悉 Radix 的概念，请不要担心，因为我们将一路回顾基本的构建模块。

# 基本设置

我们的第一步是对您进行设置，以便您可以开始用 Radix 构建您的第一个 Java DApp。

# 安装

你可以通过 gradle:
安装`radixdlt-java`库

```
repositories {
    maven { url 'https://jitpack.io' }
} 
```

```
implementation 'com.radixdlt:radixdlt-java:0.9.3' 
```

# 推荐

对于本指南，我们将假设您对 Java 有所了解，但是即使您来自不同的编程语言，您也应该能够理解。

> 如果需要复习 Java，我们推荐阅读这本[指南](https://www.learnjavaonline.org/)。

我们还假设您熟悉函数、对象、数组和类等编程概念。此外，反应模式和可观察模式的知识是有帮助的，但不是必需的。

# 概述

现在您已经设置好了，请随意查看我们的词汇表，这样我们就可以共享一种通用语言:

- [宇宙](https://docs.radixdlt.com/alpha/learn/glossary#universe)
- [碎片](https://docs.radixdlt.com/alpha/learn/glossary#shard)-[节点](https://docs.radixdlt.com/alpha/learn/glossary#nodes)-
-[原子](https://docs.radixdlt.com/alpha/learn/glossary#atoms)-
-[账号](https://docs.radixdlt.com/alpha/learn/glossary#account)-
-[地址](https://docs.radixdlt.com/alpha/learn/glossary#address)-
-[身份](https://docs.radixdlt.com/alpha/learn/glossary#identity)

# 打造聊天机器人

现在我们已经对 Radix 背后的概念做了一个简要的概述，并且我们共享一个公共语言，我们准备开始使用`radixdlt-java`库构建我们的示例聊天机器人 DApp。

# 连接网络

在我们可以与分类帐交互之前，第一步是选择我们想要连接到哪个[宇宙](https://docs.radixdlt.com/alpha/develop/java-client-library-guide/get-started#universe)。在这种情况下，我们将使用 ALPHANET universe 配置，因为它是我们的主要测试环境。

我们首先创建一个聊天机器人类，并使用`RadixUniverse.bootstrap()`方法:
初始化到基数宇宙的连接

```
import com.radixdlt.client.core.RadixUniverse;

public class ChatBot {
    static {
        RadixUniverse.bootstrap(Bootstrap.ALPHANET);
    }

    public ChatBot() {
    }
} 
```

# 创建基数标识

接下来，我们需要生成一个`RadixIdentity`，它将代表聊天机器人处理私有/公共密钥逻辑。我们通过调用`SimpleRadixIdentity()`构造函数:
来实现

```
public class ChatBot {
    static {
        RadixUniverse.bootstrap(Bootstrap.ALPHANET);
    }

    public ChatBot() throws IOException {
        // Load Identity
        final RadixIdentity chatBotIdentity = new SimpleRadixIdentity("chatbot.key");
    }
} 
```

> 注意:`thechatbot.key`文件是保存和加载私钥的地方。

# 创建基数地址

现在，使用身份的公钥，我们可以生成一个唯一的基数地址，这是聊天机器人在基数宇宙中的唯一标识符。

```
public class ChatBot {
    static {
        RadixUniverse.bootstrap(Bootstrap.ALPHANET);
    }

    private final RadixAddress address;

    public ChatBot() throws IOException {
        // Load Identity
        final RadixIdentity chatBotIdentity = new SimpleRadixIdentity("chatbot.key");
        final ECPublicKey publicKey = chatBotIdentity.getPublicKey();
        this.address = RadixUniverse.getInstance().getAddressFrom(publicKey);
    }
} 
```

# 接收消息

一旦我们为聊天机器人创建了地址，我们就可以开始接收消息了。要获得信息，我们需要:

-初始化 Radix 消息模块
-为我们的地址订阅一个监听器
-解密每条消息(使用我们的身份)
-打印我们收到的每条消息

```
public class ChatBot {
    static {
        RadixUniverse.bootstrap(Bootstrap.ALPHANET);
    }

    private final RadixAddress address;

    public ChatBot() throws IOException {
        // Load Identity
        final RadixIdentity chatBotIdentity = new SimpleRadixIdentity("chatbot.key");
        final ECPublicKey publicKey = chatBotIdentity.getPublicKey();
        this.address = RadixUniverse.getInstance().getAddressFrom(publicKey);

        // Subscribe/Decrypt messages
        final Observable<RadixMessage> messageObservables = RadixMessaging.getInstance()
            .getAllMessagesDecrypted(chatBotIdentity);

        // Print messages
        messageObservables.subscribe(System.out::println);
    }
} 
```

# 发送消息

既然聊天机器人可以接收消息，我们可以通过向发送者发送消息来让机器人回复。我们通过以下方式做到这一点:
为另一个侦听器订阅我们的地址
过滤消息(这样我们就不会对自己的消息做出响应)
创建并向原始发送者发送加密的回显消息

```
public class ChatBot {
    static {
        RadixUniverse.bootstrap(Bootstrap.ALPHANET);
    }

    private final RadixAddress address;

    public ChatBot(Function<String,String> chatBotAlgorithm) {
        // Load Identity
        final RadixIdentity chatBotIdentity = new SimpleRadixIdentity("chatbot.key");
        final ECPublicKey publicKey = chatBotIdentity.getPublicKey();
        this.address = RadixUniverse.getInstance().getAddressFrom(publicKey);

        // Subscribe/Decrypt messages
        final Observable<RadixMessage> messageObservables = RadixMessaging.getInstance()
                                                                    .getAllMessagesDecrypted(chatBotIdentity);

        // Print messages
        messageObservables.subscribe(System.out::println);

        // Chatbot replies
        messageObservables
            .filter(message -> !message.getFrom().equals(address)) // Don't reply to ourselves!
            .filter(message -> Math.abs(message.getTimestamp() - System.currentTimeMillis()) < 60000) // Only reply to recent messages
            .subscribe(message ->
                RadixMessaging.getInstance()
                    .sendMessage(chatBotAlgorithm.apply(message.getContent()), chatBotIdentity, message.getFrom());
    }
} 
```

# 点睛之笔

至此，我们已经拥有了简单的聊天机器人 DApp 的所有基本构件。现在，为了有一个完整和功能的 DApp，让我们添加一些收尾工作:

-使用一个`Function<String,String>`来表示聊天机器人算法，使其易于扩展
-构建一个`main`方法，通过向其地址
发送消息来运行和测试

```
import com.radixdlt.client.core.Bootstrap;
import com.radixdlt.client.core.RadixUniverse;
import com.radixdlt.client.core.address.RadixAddress;
import com.radixdlt.client.core.crypto.ECPublicKey;
import com.radixdlt.client.core.identity.OneTimeUseIdentity;
import com.radixdlt.client.core.identity.RadixIdentity;
import com.radixdlt.client.core.identity.SimpleRadixIdentity;
import com.radixdlt.client.core.network.AtomSubmissionUpdate;
import com.radixdlt.client.messaging.RadixMessage;
import com.radixdlt.client.messaging.RadixMessaging;
import io.reactivex.Observable;
import io.reactivex.ObservableSource;
import java.util.function.Function;

public class ChatBot {
    static {
        RadixUniverse.bootstrap(Bootstrap.ALPHANET);
    }

    private final RadixAddress address;

    public ChatBot(Function<String,String> chatBotAlgorithm) throws Exception {
        // Load Identity
        final RadixIdentity chatBotIdentity = new SimpleRadixIdentity("chatbot.key");
        final ECPublicKey publicKey = chatBotIdentity.getPublicKey();
        this.address = RadixUniverse.getInstance().getAddressFrom(publicKey);

        // Subscribe/Decrypt messages
        final Observable<RadixMessage> messageObservables = RadixMessaging.getInstance()
            .getAllMessagesDecrypted(chatBotIdentity);

        // Print messages
        messageObservables.subscribe(System.out::println);

        // Chatbot replies
        messageObservables
            .filter(message -> !message.getFrom().equals(address)) // Don't reply to ourselves!
            .filter(message -> Math.abs(message.getTimestamp() - System.currentTimeMillis()) < 60000) // Only reply to recent messages
            .flatMap((io.reactivex.functions.Function<RadixMessage, ObservableSource<AtomSubmissionUpdate>>) message ->
                RadixMessaging.getInstance().sendMessage(chatBotAlgorithm.apply(message.getContent() + " from Bot!"), chatBotIdentity, message.getFrom())
            ).subscribe(System.out::println, Throwable::printStackTrace);
    }

    public static void main(String[] args) throws Exception {
        ChatBot chatBot = new ChatBot(Function.identity());
        System.out.println("Chatbot address: " + chatBot.address);

        // Send message to bot
        RadixIdentity oneTimeUseIdentity = new OneTimeUseIdentity();
        RadixMessaging.getInstance()
            .sendMessage("Hello World", oneTimeUseIdentity, chatBot.address)
            .subscribe(System.out::println);
    }
} 
```

运行该命令将产生类似如下的输出:

```
Chatbot address: JGX2vijpLPBqTbtbznT81MGM7vVSDNDT95xHuChVdHnGzk9vBie
JGePib7zXBWa4ExNkpCmMbfkNy8H6UP351gMsdoYMgsrGUC43aB -> JGX2vijpLPBqTbtbznT81MGM7vVSDNDT95xHuChVdHnGzk9vBie: Hello World
JGX2vijpLPBqTbtbznT81MGM7vVSDNDT95xHuChVdHnGzk9vBie -> JGePib7zXBWa4ExNkpCmMbfkNy8H6UP351gMsdoYMgsrGUC43aB: Hello World from Bot! 
```

恭喜你！现在，您已经创建了一个连接到 Radix 网络的聊天机器人。

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询