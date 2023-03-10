# 科特林模式:抽象工厂

> 原文：<https://dev.to/dbottillo/patterns-in-kotlin-abstract-factory-4o8>

抽象工厂模式意图是提供一个接口来创建相关或依赖对象的系列，而不指定它们的具体类。

例如:让我们想象一下，你必须在你的应用程序中集成一个聊天工具`SDK`。也许在你的公司里，你已经决定使用一个特定的`SDK`但是还没有开始后端的集成，他们也可能在某个时候换成另一个`SDK`，谁知道呢！
同时，你知道应用程序的要求，你有设计，你知道，例如，你有多个频道，每个频道包含来自你的用户和其他人的多条消息。
将聊天的实际具体实现与`UI`分开不是很好吗？最终，`UI`和你的用户都不太关心你要用哪个`SDK`)

您的应用程序应该独立于通道和消息是如何创建和组成的。理想情况下，您的应用程序应该配置有任何聊天功能`SDK`。因为无论您打算使用哪一个`SDK`，您的应用程序都需要有通道和消息。

这种分离还有另一个原因:测试。你不希望在单元测试期间产生一个包含真实消息的真实通道！你可以伪造你的聊天，使它只是通过这种模式提供的另一个。
还可以加快开发:刚开始的时候，当你不知道一个具体的 SDK 会是什么样子的时候，你可以开始定义接口，有一个假的聊天实现，直到真的那个进来。

首先，让我们定义应用程序中的聊天应该是什么样子:

```
interface Chat {
    fun getChannels(): List<Channel>
    fun getMessages(channelId: Long): List<Message>
}
data class Channel(val id: Long, 
                   val name: String, 
                   val users: List<User>)
data class Message(val id: Long, 
                   val text: String)
sealed class User {
    object Self: User()
    class Other(val name: String): User()
} 
```

对于一个聊天对象，我们希望能够获得它的所有通道，并获取特定通道的消息。一个通道还包含所有可能使用我们的应用程序`Self`的用户和所有`Other`参与者。太好了！

需要指出的是，我们不应该考虑任何特定的实现，这个接口是基于我们的用例建模的，任何特定的聊天实现都必须以这种方式公开数据。

现在我们知道了聊天是什么样子，让我们定义创建聊天的工厂:

```
abstract class ChatFactory {
   abstract fun getChat(): Chat
} 
```

工厂现在只需要一个方法来在运行时生成一个聊天实例。

每个具体工厂都必须生成一个特定的聊天实例:

```
class FakeChatFactory: ChatFactory() {
    override fun getChat() = TODO()
}
class SDKChatFactory: ChatFactory() {
    override fun getChat() = TODO()
} 
```

使用这种模式，现在很容易切换实现，您可以用一个工厂替换另一个工厂！例如，让我们尝试创建一个 FakeChat:

```
class FakeChatFactory: ChatFactory() {
    override fun getChat() = FakeChat()
}
class FakeChat: Chat {
        override fun getChannels(): List<Channel> {
            return listOf(Channel(id = 1, 
                                  name = "First Channel", 
                                  users = listOf(User.Self, User.Other("Tom"))))
        }
        override fun getMessages(channelId: Long): List<Message>{
            return listOf(Message(id = 1, text = "Hello"),
                          Message(id = 2, text = "How are you?"))
        }
} 
```

很好，当然这是一个 FakeChat，所以一切都是假的:)但是正如你所看到的，我们可以很容易地用一些数据假装聊天，或者我们可以使用这个 FakeChat 来创建一个聊天实例，用于测试目的，例如验证不同的场景。

当使用工厂时，调用者并不真正知道数据来自哪里，我们在运行时使用的是哪一个具体的实现。此外，在一个更复杂的场景中，每个具体的实现都是紧密耦合的:例如，你不能混合使用真实的通道和虚假的消息。每个具体的实现都必须提供与聊天界面定义相匹配的数据。

因此，现在在运行时，我们可以:

```
val channels = FakeChatFactory().getChat().getChannels()
print(channels) 
```

它将打印:

```
[Channel(id=1, name=First Channel, 
         users=[User$Self@31befd9f, User$Other@1c20c684])] 
```

集成一个真正的聊天 SDK 将需要实现一个特定的聊天接口(如 TwilioChatFactory)，该接口将基于特定的 SDK 实现创建聊天对象。

要点:抽象工厂模式中的另一种常见方法是在创建工厂时指定类型:

```
abstract class ChatFactory {
   abstract fun getChat(): Chat

    companion object {
        inline fun <reified T : Chat> create(): ChatFactory =
        when (T::class) {
            FakeChat::class -> FakeChatFactory()
            SDKChat::class  -> SDKChatFactory()
            else            -> throw IllegalArgumentException()
        }
    }
} 
```

因此，现在你不必知道每个工厂的确切名称，你只需确定具体的聊天类型:

```
ChatFactory.create<FakeChat>().getChat().getChannels()
ChatFactory.create<TwilioChat>().getChat().getChannels() 
```

概括地说，抽象工厂模式应该在以下情况下使用:

*   一个系统应该独立于它的产品是如何被创建、组成和表现的
*   一个系统应该配置多个产品系列中的一个
*   一系列相关的产品对象被设计为一起使用，您需要实施该约束
*   您希望提供产品的类库，并且希望只显示它们的接口，而不是它们的实现