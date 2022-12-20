# corda——扩展流程以定制交易验证

> 原文：<https://dev.to/lankydandev/corda-extending-flows-to-customise-transaction-validation-5akj>

通过使用流扩展，运行相同 CorDapp 的节点可以包含额外的验证，以确保事务满足它们的特定需求。合同内部的验证集中在所有交易方必须遵守的规则上。正因为如此，它们更加通用，并专注于确保没有人将无效的事务放在一起。这省去了单个组织需要的任何检查。通过在组织之间提供一个能够添加更多检查的基础 CorDapp，他们可以各自定制 CorDapp 以满足他们的需求。流动延伸使这成为可能。继续推动组织通过通用 CorDapps 一起通信，同时仍然提供足够的定制以满足他们的每个特定需求。

该介绍可能已经引起了您的注意(我希望如此)，但是实现这一点的代码相对简单。它遵循了我在[中提到的从外部 cordapp](https://lankydan.dev/extending-and-overriding-flows-from-external-cordapps)扩展和覆盖流程的相同概念。我一直在谈论的额外事务验证可以通过在流或响应者流中提供一个单独的`open`函数来实现。

我将跳过以下片段中的许多信息，因为它们在[扩展和覆盖来自外部 CorDapps 的流](https://lankydan.dev/extending-and-overriding-flows-from-external-cordapps)中有所涉及。如果你现在还没有意识到这一点，我强烈建议你阅读这篇文章。

对于启动流程中的额外验证，我相信类似下面例子的基本流程是您想要的:

```
@InitiatingFlow
open class SendMessageFlow(private val message: MessageState) :
  FlowLogic<SignedTransaction>() {

  open fun extraTransactionValidation(transaction: TransactionBuilder) {
    // to be implemented by subtype flows - otherwise do nothing
  }

  @Suspendable
  final override fun call(): SignedTransaction {
    // build transaction
    val tx = verifyAndSign(transaction)
    // collect signatures
    // save transaction
  }

  private fun verifyAndSign(transaction: TransactionBuilder): SignedTransaction {
    extraTransactionValidation(transaction)
    transaction.verify(serviceHub)
    return serviceHub.signInitialTransaction(transaction)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在运行任何共享事务验证之前，在`TransactionBuilder`的`verify`函数之前调用`extraTransactionValidation`会强制提议的事务满足组织的个人要求。

扩展流程提供了`extraTransactionValidation`的实现。它的实现可能类似于:

```
class ExtraValidationSendMessageFlow(message: MessageState) :
  SendMessageFlow(message) {

  override fun extraTransactionValidation(transaction: TransactionBuilder) {
    requireThat {
      val messages = transaction.toLedgerTransaction(serviceHub).outputsOfType<MessageState>()
      "There must be only one output message" using (messages.size == 1)
      val message = messages.single()
      "Message must contain the secret passphrase" using (message.contents.contains("I love Corda"))
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这与你在合同中看到的确认方式非常相似。这才是重点。验证以同样的方式完成，但是检查交易的规则是定制的。

以类似于启动流程的方式。基本响应器流将类似于下面的代码片段:

```
@InitiatedBy(SendMessageFlow::class)
open class SendMessageResponder(private val session: FlowSession) : FlowLogic<SignedTransaction>() {

  open fun extraTransactionValidation(stx: SignedTransaction) {
    // to be implemented by subtype flows - otherwise do nothing
  }

  @Suspendable
  final override fun call(): SignedTransaction {
    val stx = subFlow(object : SignTransactionFlow(session) {
      override fun checkTransaction(stx: SignedTransaction) {
        extraTransactionValidation(stx)
      }
    })
    // save transaction
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

将`extraTransactionValidation`添加到`SignTransactionFlow.checkTransaction`的主体非常有意义。`checkTransaction`及随后的`extraTransactionValidation`在`SignTransactionFlow`执行的总承包确认之前运行。

下面是一个扩展基本流程并实现`extraTransactionValidation` :
的示例流程

```
@InitiatedBy(SendMessageFlow::class)
class ExtraValidationSendMessageResponder(session: FlowSession) :
  SendMessageResponder(session) {

  override fun extraTransactionValidation(stx: SignedTransaction) {
    requireThat {
      val messages = stx.coreTransaction.outputsOfType<MessageState>()
      "There must be only one output message" using (messages.size == 1)
      val message = messages.single()
      "Message must contain the secret passphrase" using (message.contents.contains("I love Corda"))
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，这个验证也包含在启动流程中。这就是我所说的。通过将这种验证添加到发起和响应流程中，组织可以确保通过其系统的任何交易都符合其精确的要求。

由于这种验证的性质，它可以被移到流之间共享的函数中:

```
private fun validate(transaction: BaseTransaction) {
  requireThat {
    val messages = transaction.outputsOfType<MessageState>()
    "There must be only one output message" using (messages.size == 1)
    val message = messages.single()
    "Message must contain the secret passphrase" using (message.contents.contains("I love Corda"))
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个 Kotlin 函数已经从一个类中移出，成为一个静态函数。然后，它可以在两个流中使用。

在结束这篇文章之前，我想进一步探讨的一点是启动流程中的附加验证。很有可能由一个组织提出的交易会被该组织认为是有效的。所以问题是，为什么要在流程中增加额外的检查呢？类似的验证可以在流程被调用之前完成。这是一个非常合适的地方。当使用第三方 CorDapp 时，将它添加到流程中的好处就体现出来了。随着 CorDapp 复杂性的增加，这种情况会进一步加剧。确定由复杂流程创建的状态可能不是一件简单的事情。因此，创建并立即检查事务的内容是最安全的做法。

总之，在流程中放置额外的验证可以确保交易组织在提交交易之前对交易内容完全满意。此外，它继续推动组织使用由第三方开发者实现的 CorDapps。通过在 CorDapps 中为组织提供足够的灵活性来添加他们自己的规则。由于交易满足网络中任何组织的要求的保证，采用它们变得更加容易。

这篇文章中使用的其余代码可以在我的 [Github](https://github.com/lankydan/corda-extendable-flows/tree/extra-validation-logic-in-responder-flow) 中找到。

如果你喜欢这篇文章或者觉得它有帮助(或者两者兼而有之),那么请随时在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) ,并且记得与其他任何可能发现这篇文章有用的人分享！