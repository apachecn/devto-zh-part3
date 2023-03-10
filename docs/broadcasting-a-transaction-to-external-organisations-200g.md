# Corda -向外部组织广播交易

> 原文：<https://dev.to/lankydandev/broadcasting-a-transaction-to-external-organisations-200g>

有一种误解认为 Corda 不能通过网络广播数据。这是完全错误的。事实上，Corda 可以在网络中的节点之间发送任何东西。Corda 没有做的是与节点共享不必要的数据(事务),这些数据与个体交互无关。默认情况下，隐私是 Corda 设计的核心组成部分。与其他 DLT(分布式账本技术)平台和区块链相比，这确实是一个很大的区别。与非交易方共享数据可能不是 Corda 默认行为的一部分，但绝对在其能力范围之内。在这篇文章中，我将演示向任何期望的节点发送事务所需的少量代码。

也就是说，这篇文章中包含的代码只是一个简单的实现。展望未来，我的目标是在这个主题上写更多的文章，并朝着更复杂、更有用的广播事务实现前进。

在向您展示任何代码之前，我们应该简短地讨论一下向最初没有参与事务的各方广播数据的必要性。我遇到的最常见的原因是需要满足监管要求。对于某些工作流，信息必须与第三方共享。然后，这些团体可以确认没有任何可疑的事情发生，也可能是与真实世界的联系。我不想撒谎。这不是我的专业领域。幸运的是，如果您正在阅读这篇文章是因为您对满足监管要求有所顾虑，那么您可能比我更清楚为什么您需要广播交易的能力。

在[文档](https://docs.corda.net/tutorial-observer-nodes.html)中也有关于这个主题的信息。

到代码上。下面是一个向任何传递给它的`Party`发送一个`SignedTransaction`的流:

```
@InitiatingFlow
class BroadcastTransactionFlow(
  private val stx: SignedTransaction,
  private val recipients: List<Party>
) : FlowLogic<Unit>() {

  @Suspendable
  override fun call() {
    for (recipient in recipients) {
      val session = initiateFlow(recipient)
      subFlow(SendTransactionFlow(session, stx))
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个场景中，Corda 从平台内部完成了几乎所有的繁重工作。因此，您唯一需要做的事情就是遍历传入的各方，并将事务发送给它们中的每一个。

对`SendTransactionFlow`的每个调用都将与下面的响应者流进行通信:

```
@InitiatedBy(BroadcastTransactionFlow::class)
class BroadcastTransactionResponder(private val session: FlowSession) : FlowLogic<Unit>() {

  @Suspendable
  override fun call() {
    subFlow(ReceiveTransactionFlow(session, statesToRecord = StatesToRecord.ALL_VISIBLE))
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`call`里面只有一行。所以，由于那里没有其他东西，它一定很重要。`ReceiveTransactionFlow`是`SendTransactionFlow`的对应方，它接收并保存发送给它的事务。此外，`statesToRecord`属性确定事务中的哪些状态应该存储在保险库中。由于这是一次广播(可能出于监管原因)，交易的内容将会很重要。为了实现这一点，`StatesToRecord.ALL_VISIBLE`被用来告诉`ReceiveTransactionFlow`记录包含在事务中的每个状态。根据您的用例，如果您不需要所有的事务状态，那么您可以使用`ONLY_RELEVANT`甚至`NONE`。

这些代码片段中最后一行重要的代码是`BroadcastTransactionFlow`上的`@InitiatingFlow`注释。注释允许流工作，而不需要其调用者创建会话，也不需要在将会话传递给流之前创建会话。流程会处理这个问题。从逻辑上讲，这是有意义的，因为在大多数情况下，您不会与尚未存储事务的各方发起会话。由于这一点，从另一个内部使用这个流是很好和简单的。例如，它可以添加在流程的末尾:

```
subFlow(FinalityFlow(stx, sessions)).also {
  // sends to everyone in the network
  val broadcastToParties =
    serviceHub.networkMapCache.allNodes.map { node -> node.legalIdentities.first() } - message.recipient - message.sender
  subFlow(BroadcastTransactionFlow(it, broadcastToParties))
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，调用`BroadcastTransactionFlow`后，交易将存在于传递给流的每一方的金库中。这包括事务中包含的状态。太好了，任务完成。您现在知道 Corda 可以广播事务。

在您复制和粘贴这段代码并将其直接交付生产之前，我需要解决一个问题。从广播事务接收输出状态的各方也有能力使用它们。因此，如果你希望分享这样的交易，那么你需要采取必要的保护措施，防止组织花费不属于他们的状态。是的，该组织可能是一个监管者，他们做任何不正当事情的可能性很低。是的，也许有可能在 Corda 之外解决这些问题。然而，这些假设都不理想。为了规避这一点，你的合同和流程必须设计成防止组织花费不属于他们的州。这个责任落在了 CorDapp 开发者的身上。不是科达。Corda 赋予您的灵活性必须加以控制，以防止不良后果。我将在以后的文章中讨论这个话题。

总而言之，Corda 不能向最初没有参与该过程的各方广播交易是一个神话。你可以通过使用我在这篇文章中展示的代码在你的应用程序中实现这一点。这不是最快的实现方式。但这很有效。允许与需要网络上发生的交易细节的组织共享数据，这些组织不包括在单个交易本身中。

如果你喜欢这篇文章或者觉得它有帮助(或者两者兼而有之),那么请随时在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) ,并且记得与其他任何可能发现这篇文章有用的人分享！