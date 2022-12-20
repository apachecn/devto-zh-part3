# Go:使用成员列表创建分布式系统

> 原文：<https://dev.to/davidsbond/golang-creating-distributed-systems-using-memberlist-2fa9>

### 简介

随着整个企业软件的伸缩需求的稳步增长，创建分布式系统的需求也在增长。导致了依赖于分布式架构的各种难以置信的可扩展产品。维基百科将分布式系统描述为:

> 一种系统，其组件位于不同的联网计算机上，这些计算机通过相互传递消息来进行通信并协调它们的操作。

这些系统的例子从数据存储到事件总线等等。分布式系统有许多应用。因为有如此多的应用，这些分布式通信协议也有许多现成的实现，使我们能够轻松地构建自我发现的分布式系统。这篇文章旨在详细介绍 [memberlist](https://github.com/hashicorp/memberlist) 包，并演示如何使用它开始构建一个分布式系统。

我目前已经使用这个库创建了 [sse-cluster](https://github.com/davidsbond/sse-cluster) ，一个可伸缩的[服务器发送事件](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events)代理。它利用 memberlist 来发现新节点并将事件传播到分布在不同节点上的客户端。它诞生于扩展现有 SSE 实现的需要。这是使用该软件包的一个相当不错的参考。我还没有深入研究配置的微调方面。

那么什么是 [memberlist](https://github.com/hashicorp/memberlist) ？

> Memberlist 是一个 Go 库，它使用基于 gossip 的协议管理集群成员资格和成员故障检测。

听起来不错，但是什么是基于八卦的协议呢？

想象一下，一个开发团队喜欢散布关于他们同事的谣言。假设开发人员每小时都聚集在饮水机旁(或者一些同样平庸的办公场所)。每个开发者随机地与另一个结对，并互相分享他们的新谣言。

在这一天的开始，克里斯开始了一个新的谣言:对亚历克斯说，他认为米克的工资是其他人的两倍。在下一次会议上，Alex 告诉了 Marc，而 Chris 向 David 重复了这个想法。在每一次会面后，听到谣言的开发者的数量会翻倍(除了谣言已经通过另一个开发者被听到并且已经被有效地传播了两次的情况)。分布式系统通常以一种随机“对等选择”的形式来实现这种类型的协议:在给定的频率下，每台机器随机选择另一台机器，并共享任何热门的、辛辣的谣言。

这是对 gossip 协议如何实现的粗略描述。memberlist 包利用了 [SWIM](https://prakhar.me/articles/swim/) ，但是已经被修改以提高传播速度、收敛速度和面对处理问题(如网络延迟)时的一般健壮性。 [Hashicorp](https://www.hashicorp.com/) 发布了一篇名为[救生员:带着情境意识游泳](https://arxiv.org/abs/1707.00788)的论文，详细介绍了这些修改。

通过这个包，我们能够创建一个具有自我意识的节点集群，它可以执行我们认为合适的任何任务。

### 创建简单的集群

首先，我们需要定义我们的配置。该软件包包含一些方法，用于根据您打算在其中运行集群的环境来生成默认配置。他们在这里:

*   [默认配置](https://github.com/hashicorp/memberlist/blob/master/config.go#L226)(最适合本地网络):
    *   使用主机名作为节点名
    *   使用`7946`作为八卦交流的端口
    *   TCP 超时时间为 10 秒
*   [DefaultLocalConfig](https://github.com/hashicorp/memberlist/blob/master/config.go#L283) (最适合环回环境):
    *   基于`DefaultLANConfig`
    *   有 1 秒的 TCP 超时
*   [DefaultWANConfig](https://github.com/hashicorp/memberlist/blob/master/config.go#L267) (最适合 WAN 环境中的节点):
    *   基于`DefaultLANConfig`
    *   有 1 秒的 TCP 超时

我们将在开发机器上运行一个 3 节点集群，因此我们目前只需要`DefaultLocalConfig`。我们可以这样初始化:

```
config := memberlist.DefaultLocalConfig()

list, err := memberlist.Create(c)

if err != nil {
  panic(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果需要，我们还可以为集群中的每个节点广播一些自定义元数据。如果您希望在节点之间使用稍微不同的配置，但仍然希望它们进行通信，这将非常有用。这并不影响 memberlist 本身的操作，但是可以在基于它构建应用程序时使用。

```
node := list.LocalNode()

// You can provide a byte representation of any metadata here. You can broadcast the
// config for each node in some serialized format like JSON. By default, this is
// limited to 512 bytes, so may not be suitable for large amounts of data.
node.Meta = []byte("some metadata") 
```

Enter fullscreen mode Exit fullscreen mode

这使我们可以运行单节点集群。为了加入一个现有的集群，我们可以使用`list.Join()`方法连接到一个或多个现有的节点。我们可以扩展上面的例子来连接到一个现有的集群。

```
// Create an array of nodes we can join. If you're using a loopback
// environment you'll need to make sure each node is using its own
// port. This can be set with the configuration's BindPort field.
nodes := []string{
  "0.0.0.0:7946"
}

if _, err := list.Join(nodes); err != nil {
  panic(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经成功配置了客户端并加入了一个现有集群。该软件包将输出一些日志，这样您就可以看到节点之间的同步以及它们遇到的任何错误。最重要的是，我们需要在完成后优雅地离开成员列表。如果我们不处理一个优雅的退出，集群中的其他节点将把它视为一个死节点，而不是一个已经离开的节点。

为此，我们需要监听退出应用程序的信号，捕捉它并离开集群:

```
// Create a channel to listen for exit signals
stop := make(chan os.Signal, 1)

// Register the signals we want to be notified, these 3 indicate exit
// signals, similar to CTRL+C
signal.Notify(stop, syscall.SIGINT, syscall.SIGTERM, syscall.SIGHUP)

<-stop

// Leave the cluster with a 5 second timeout. If leaving takes more than 5
// seconds we return.
if err := ml.Leave(time.Second * 5); err != nil {
  panic(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

### 成员之间的交流

现在我们可以加入和离开集群，我们可以使用成员列表来执行分布式操作。

让我们创建一个简单的消息传递系统。我们可以在单个节点上通过 HTTP 接收消息，并将其传播到集群中的下一个节点。这给了我们一个最终一致的系统，可以适应某种事件总线。

这绝不是一个最佳的解决方案，但是展示了集群环境中服务发现的强大功能。

先说一个节点:

```
type (
  // The Node type represents a single node in the cluster, it contains
  // the list of other members in the cluster and an HTTP client for
  // directly messaging other nodes.
  Node struct {
    memberlist *memberlist.Memberlist
    http *http.Client
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

假设这个节点从 HTTP 处理程序接收到一条消息，该处理程序接收整个请求体，并将其转发给另一个节点。我们可以实现一个方法来迭代列表中的成员，并尝试转发消息。一旦消息被成功转发到单个节点，它就停止处理它。这意味着我们有最终的一致性，其中**最终**所有节点接收所有消息。

```
func (n *Node) HandleMessage(msg []byte) {
  // Iterate over all members in the cluster
  for _, member := range n.memberlist.Members() {
    // We also need to make sure we don't send the message to the node
    // currently processing it
    if member == n.memberlist.LocalNode() {
      continue
    }

    // Memberlist gives us the IP address of every member. In this example,
    // they all handle HTTP traffic on port 8080\. You can also provide custom
    // metadata for your node to provide interoperability between nodes with
    // varying configurations.
    url := fmt.Sprintf("http://%s:8080/publish", member.Addr)
    resp, err := n.http.Post(url, "application/json", bytes.NewBuffer(msg))

    if err != nil {
      // handle error and try next node
      continue
    }

    if resp.StatusCode != http.StatusOK {
      // handle unexpected status code and try next node
      continue
    }

    // Otherwise, we've forwarded the message and can do
    // something else.
    break
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

希望这篇文章概述了如何使用`memberlist`包实现集群应用程序。这个库非常强大，允许您关注集群所依赖的实际逻辑，而不是底层的网络基础设施。根据我的经验，成员间同步所花费的时间可以忽略不计，但是你应该记住协议是最终的。

在上面的例子中，如果节点加入/离开的流量很大，我们不能保证我们的消息会传播到每个节点。理想情况下，新成员应该以可控的方式加入，并且只在必要时加入。

### 链接

*   [https://en.wikipedia.org/wiki/Distributed_computing](https://en.wikipedia.org/wiki/Distributed_computing)
*   [https://github.com/hashicorp/memberlist](https://github.com/hashicorp/memberlist)
*   [https://github.com/davidsbond/sse-cluster](https://github.com/davidsbond/sse-cluster)
*   [https://developer . Mozilla . org/en-US/docs/Web/API/Server-sent _ events/Using _ Server-sent _ events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events)
*   [https://prakhar.me/articles/swim/](https://prakhar.me/articles/swim/)
*   [https://www.hashicorp.com/](https://www.hashicorp.com/)
*   [https://arxiv.org/abs/1707.00788](https://arxiv.org/abs/1707.00788)