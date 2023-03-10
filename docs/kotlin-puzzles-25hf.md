# 科特林拼图

> 原文：<https://dev.to/tsalikispk/kotlin-puzzles-25hf>

# 解谜前的一些语境

我一直想读完的一本书是史蒂夫·弗里曼和纳特·普莱斯写的《测试引导下的面向对象的成长》。在他们的优秀著作中，作者不仅描述了如何使用测试来驱动系统的设计，而且还提供了一个全面的例子来展示他们在实践中所宣扬的东西。

这个例子是一个 Swing 应用程序，它接收并向 XMPP(发布/订阅协议)服务器发送事件，并使用一些过时的测试库(Windowlicker 用于测试 Swing GUI，JMock 用于测试模拟)。尽管所有代码都是用 Java 编写的，我还是决定尽量少用 Kotlin，以便专注于作者提供的技术，并添加了 Mockito 而不是 JMock 来测试模拟。最后，我得到了一个混合代码库，其中大部分都是用 Java 编写的，在四种情况下添加了一些最少的 Kotlin 味道:

1.  用类来代替 Apache 的 commons equals/hashcode 来创建不可变的对象。
2.  `sealed`类而不是`enums`
3.  更多惯用的 Kotlin 特性，比如用`map`代替`for`循环。
4.  一些 Kotlin 代码中的计算属性而不是函数。

总的来说，我想避免大量使用 Kotlin 来避免不愉快的意外，但意外还是发生了，特别是上面列表中的第 3 和第 4 项。

# 难题 1:分裂的问题

在他们的例子中，作者有一个解析来自网络的类似 CSV 的响应的类。响应看起来像这样:

```
"Event: PRICE; Price: 100; Increment: 20; Bidder: Someone;" 
```

Enter fullscreen mode Exit fullscreen mode

他们用下面的代码片段解析响应:

```
private Map<String, String> unpackEventFrom(Message message) {
        Map<String, String> event = new HashMap<>();
        for (String element : message.getBody().split(";")) {
            String[] pair = element.split(":");
            event.put(pair[0].trim(), pair[1].trim());
        }
        return event;
    } 
```

Enter fullscreen mode Exit fullscreen mode

在这个代码片段中，他们首先在`;`上拆分字符串，然后使用拆分`:`创建对，最后他们将键和值放在一个映射中，非常简单。这种代码可以使用一些科特林的味道，所以让我们重新措辞:

```
messageBody.split(";")
        .associateTo(auctionEvent.fields) {
                val pair = it.split(":")
                pair[0].trim() to pair[1].trim()
        } 
```

Enter fullscreen mode Exit fullscreen mode

Kotlin 片段在`;`上拆分消息，然后通过在`:`上拆分创建一个对，并将该对关联到一个空地图(为了简洁起见，省略了`auctions.fields` val)。代码首先是用 Java 写的，测试通过了，但是当我把它转换成 Kotlin 时，有一个惊喜，特别是一个`IndexOutOfBounds`代码！这是怎么发生的？看起来 Kotlin 的`split`函数的实现与 Java 不同。我不知道的是，Java 的 split 也有一个`limit`参数，当调用`split(;)`时，它实际上会调用`split(;, 0)`。Koltin 也做了同样的事情，但是不同的是零在两种语言中的解释方式。

直接引自 Java 的文档:

> 如果 limit 为零，那么该模式将被应用尽可能多的次数，数组可以有任何长度，**和尾随的空字符串将被丢弃**。

我们可以看到 Java 对参数`limit`赋予了额外的含义。相反，Kotlin 让用户只应用非负的限制，并且不丢弃尾随的空格，除非你明确地告诉它这样做。

在 Kotlin 中，确切的对等词应该是这样的:

```
messageBody.split(";")
        .dropLastWhile { it.isEmpty() } 
```

Enter fullscreen mode Exit fullscreen mode

为什么科特林的创造者会这样做？有人可能会认为 Java 在这里是错误的，因为 limit 参数不仅指定了分隔符应该应用的次数，还添加了一个完全不相关的不同含义，并将零视为特殊值。关于 Kotlin 的`split`不同于 Java 的另一个很好的解释可以在[这个 stackoverflow 答案](https://stackoverflow.com/questions/48697300/difference-between-kotlin-and-java-string-split-with-regex)中找到。

# 第二个谜题:这不是你要寻找的(计算的)属性

解析的代码片段来自一个事件类。该示例的作者决定不公开地图，而是创建方法来公开事件的值。上面的`unpack`方法充当了工厂方法，初始化了地图，各种方法公开了事件的值。在我的 Kotlin 风格的事件中，我创建了一个伴随对象和私有构造函数来模拟工厂方法，并决定使用计算属性。一开始我尝试了这样的东西:

```
class Event private constructor() {

        private val fields = mutableMapOf<String, String>()

        val type = parse("Event")

        private fun parse(key: String) = fields.get(key)!!

        companion object {
            fun from(message: Message): Event {
                val auctionEvent = Event()
                messageBody.split(";")
                    .dropLastWhile { it.isEmpty() }
                    .associateTo(auctionEvent.fields) {
                        val pair = it.split(":")
                        pair[0].trim() to pair[1].trim()
                    }
                return auctionEvent
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

唉，我认为属性`type`会在映射中填充了`from`函数的值后实际解析映射中的键，这是大错特错了。由于事件是在填充地图之前创建的，`type`属性将立即解析来自地图的键，并且由于地图在构造时将是空的，该值将是 null，并且将抛出一个光荣的`kotlin.KotlinNullPointerException`，因为解析方法使用 not null `!!`操作符获取键。`type`字段看起来是一个计算属性，但实际上不是，显然应该声明如下:

```
val type
        get() = parse("Event") 
```

Enter fullscreen mode Exit fullscreen mode

尽管这种惊讶完全是我自己的错，但它让我想到，也许最终声明一个函数而不是一个计算属性会更好。计算属性的使用肯定是 Kotlin 的一种更惯用的用法，但另一方面，我觉得属性应该充当对象的状态，在这个示例中，对象的状态是地图，我们只是在地图值的转换上公开它的值，这应该是公开的函数。当然，我并不反对使用计算属性，这只是一个个人失败的抱怨。

# 最后的想法

正如我们所看到的，即使我们试图尽量减少使用惯用的 Kotlin，或者只是试图将 Java 代码移植到 Kotlin，也容易犯错误。当然，每当我们试图学习一门新的语言时，这种情况都会发生，但是将 Java 移植到 Kotlin 就更加困难了，因为即使这两种语言看起来有相似的 API，但实际上它们的实现可能是不同的。谨慎是必要的，但在我给出的例子中，我碰巧很幸运，因为 TDD 的使用提供了一个很好的回归套件，它立即发现了我的错误——事实上，这些错误是我自己的错误，因为我没有先检查 Kotlin 的文档就对 API 和习惯用法做出了假设！

Gabriel Crismariu 在 [Unsplash](https://unsplash.com/) 上拍摄的照片