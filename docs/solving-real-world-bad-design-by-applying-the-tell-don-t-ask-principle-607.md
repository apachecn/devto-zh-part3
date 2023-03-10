# 运用“告诉，不要问”原则解决现实世界中的糟糕设计

> 原文：<https://dev.to/guifroes/solving-real-world-bad-design-by-applying-the-tell-don-t-ask-principle-607>

当你看到糟糕的设计时，你能发现它吗？你能看出这里出了什么问题吗？

```
if (!response.getStatusCode().is2xxSuccessful()) {
    logError(response.getBody()); 
    throwException(response.getStatusCode()); 
} 
```

上面的片段是我们在野外经常看到的一个模式的例子:

```
if(someObject.getSomeAttribute()) { 
    doSomethingWith(someObject); 
} else { 
    doSomethingElse(someObject); 
} 
```

## 我为什么要在乎？

为了让我们理解问题是什么，为什么这是一个糟糕的设计，让我们来看一看全貌。下面是这段代码的出处:

```
public class PaymentProcessorClient { 
// some code removed for brevity's sake 
    public Sale processPayment(Payment payment) {
        ResponseEntity<Sale> response = rest.postForEntity("https://payment.com/payment", payment, Sale.class);

        if (!response.getStatusCode().is2xxSuccessful()) {
            logError(response.getBody()); 
            throwException(response.getStatusCode()); 
        } 
        logSuccessfulPayment(payment, response.getBody()); 
        return response.getBody(); 
    } 
} 
```

这个类就是通常所说的*客户端*，它的目的是与一些外部服务对话。在这个特殊的例子中，它负责在虚构的 payment.com*web 服务上创建一个支付。*

(顺便说一下，这是我的团队正在开发的真实产品的真实代码。我只是做了一些小改动来保护我们客户的身份，不让他们有任何风险。为了节省空间，我还删除了一些代码。这些变化不应该干扰对我在这里试图解释的概念的理解。)

让我们仔细看看 *processPayment* 方法。第一行是主要功能所在:它调用服务并将响应保存在*响应*变量中。

```
ResponseEntity<Sale> response = rest.postForEntity("https://payment.com/payment", payment, Sale.class); 
```

接下来，它检查*响应*，如果付款创建不成功，它记录一个错误并抛出一个异常:

```
if (!response.getStatusCode().is2xxSuccessful()) { 
    logError(response.getBody()); 
    throwException(response.getStatusCode()); 
} 
```

如果付款创建顺利，它会记录结果并返回响应正文。

```
logSuccessfulPayment(payment, response.getBody()); 
return response.getBody(); 
```

这段代码可以工作，但是正如我所说的，它可以设计得更好。我将展示原因和方法。

### 它导致重复

在我们的示例中，每次调用外部服务时，都必须检查响应，并根据响应的样子做一些事情。如果你有 3 个操作，如“付款”、“取消付款”和“退款”，你将有这个结构重复 3 次。

代码复制本身是不好的，但真正的问题是想法或概念的复制—*基于响应的样子，用它做一些事情*。这个概念在代码中的多个地方都有表达，因此，如果它发生变化，或者实现发生变化，您需要在所有表达它的地方都进行更改，这是非常低效和容易出错的。

这种重复也使得重用代码变得更加困难，因为概念并不是孤立的，也不是以一种独特的方式和位置来表达的。

### 它增加耦合

当我们遇到类似于:

```
response.getStatusCode().is2xxSuccessful() 
```

*PaymentProcessorClient* 类需要知道*响应*和*状态码*对象是如何实现的。它需要知道*响应*对象有一个 *getStatusCode* 方法，并且 *statusCode* 对象有一个 *is2xxSuccessful* 方法。这种关于其他对象如何实现的知识是**耦合**的一种形式。

如果其中一个对象的实现发生变化，会发生什么？如果 *is2xxSuccessful* 方法被弃用并被 *is201Created* 或类似的方法所取代，该怎么办？你必须进入你的*支付处理器客户端*代码并修改它。除了更改类的主要逻辑或职责之外，您还必须更改一个类。

这种过度和不必要的耦合(加上我上面谈到的重复问题)会产生级联效应，对一个类的一个更改会引发整个代码库的一系列更改。不难看出为什么这是危险的和非常不可取的。

### 它弄乱了代码

让我们记住 *PaymentProcessorClient* 和 *processPayment* 方法应该做什么:与外部支付服务对话并进行支付。

现在，请注意所有严格来说没有对服务付费的代码。

```
public Sale processPayment(Payment payment) { 
    ResponseEntity<Sale> response = rest.postForEntity("https://payment.com/payment", payment, Sale.class); 

    if (!response.getStatusCode().is2xxSuccessful()) { 
        logError(response.getBody()); 
        throwException(response.getStatusCode()); 
    } 

    logSuccessfulPayment(payment, response.getBody()); 
    return response.getBody(); 
} 
```

我们有代码查看响应、代码日志记录信息和代码抛出异常。所有这些都需要发生，但它们更多的是围绕或支持这门课的主要思想。它们的存在也使得阅读和理解*paymentprocessorclient . process payment 的思想变得更加困难，*使得维护这些代码变得更加困难和昂贵。

## 怎么能让这个更好？

这里的解决方案是将这两个纠缠在一起的概念分开:

概念 1:调用外部服务；

概念 2:基于服务的响应采取行动。

第一个概念是通过 *PaymentProcessorClient* 的 *processPayment* 方法实现的，我们还需要为第二个找到一个归宿。因为它涉及到查看*响应*并基于此对*响应*做一些事情，让*响应*对象实现这个概念——或者类似的东西，听起来是合理的。让我们看看它会是什么样子。

## 解

我们需要做的第一件事是将*销售*对象重命名为*销售给*。到目前为止，我们所说的销售只是我们付款后外部服务返回给我们的东西。它只是一个数据结构，因此将其重命名为 *SaleDTO* 是有意义的。

```
public SaleDTO processPayment(Payment payment) {
    ResponseEntity<SaleDTO> response = rest.postForEntity("https://payment.com/payment", payment, SaleDTO.class); 

    if (!response.getStatusCode().is2xxSuccessful()) { 
        logError(response.getBody()); 
        throwException(response.getStatusCode()); 
    } 

    logSuccessfulPayment(payment, response.getBody()); 
    return response.getBody(); 
} 
```

*SaleDTO* 只保存我们从服务中获得的数据。我们需要一个真正的对象来实现销售的概念，并具有我们想要的行为——这是以前由 *PaymentProcessorClient* 完成的。让我们创建一个新的类 *Sale* ，它将是我们从 *processPayment* 方法返回的。*销售*将由*响应实体*对象和*支付*对象构建。

```
public Sale processPayment(Payment payment) { 
    ResponseEntity<SaleDTO> response = rest.postForEntity("https://payment.com/payment", payment, SaleDTO.class); 

    if (!response.getStatusCode().is2xxSuccessful()) { 
        logError(response.getBody()); 
        throwException(response.getStatusCode()); 
    } 

    logSuccessfulPayment(payment, response.getBody()); 
    return new Sale(payment, response); 
} 
```

接下来，我们将之前在 *PaymentProcessorClient* 上的行为移到 *Sale* 上。

```
public class Sale { 

    public Sale(Payment payment, ResponseEntity<SaleDTO> saleResponse) { 
        this.saleResponse = saleResponse; 

        if (isResponseUnsuccessful()) { 
            logError(saleResponseBody()); 
            throw new Exception(saleResponseStatusCode()); 
        } 

        logSuccessfulPayment(payment, saleResponseBody()); 
    } 

    private SaleDTO saleResponseBody() { 
        return this.saleResponse.getBody(); 
    } 

    private HttpStatusCode saleResponseStatusCode() { 
        return this.saleResponse.getStatusCode(); 
    } 

    private boolean isResponseUnsuccessful() { 
        return !saleResponseStatusCode().is2xxSuccessful(); 
    } 
} 
```

最后一步是清理 *PaymentProcessorClient* 的 *processPayment* 方法:

```
public Sale processPayment(Payment payment) { 
    ResponseEntity<SaleDTO> response = rest.postForEntity("https://payment.com/payment", payment, SaleDTO.class); 
    return new Sale(response, payment); 
} 
```

我们在这里应用了[告诉，不要问](https://martinfowler.com/bliki/TellDontAsk.html)原则。我们不是问一个对象关于它的一些事情，然后用这个对象做一些事情或者要求它做一些事情，我们只是告诉这个对象我们想要什么，它应该会照顾它。在我们的例子中，我们只是告诉对象存在！

现在我们有了一个干净的 *PaymentProcessorClient* 和一个**智能** _ **Sale** _ **对象，它知道当出现问题时该做什么**。让我们看看这个设计如何解决我们描述的问题。

## 现在设计好点了吗？

重复这一概念——对不良反应做出反应——的必要性已经不复存在。它由 *Sale* 类实现，创建该类是为了捕获外部服务上所有操作的响应，比如退款和作废支付。

*PaymentProcessorClient* 和 *Response* 和 *StatusCode* 类之间的耦合消失了。那些现在被耦合到了*销售*类，这让我感觉更好，因为它们彼此更加相关。

我们通过使用[自封装](https://www.martinfowler.com/bliki/SelfEncapsulation.html)技术，将这些类的耦合性降低了一点，隔离了对私有方法的依赖。查看 *saleResponseBody* 、 *saleResponseStatusCode* 和 *isResponseUnsuccessful* 方法。

一些耦合将会一直存在，因为对象需要知道彼此的一些情况，以便协作和一起工作。

由于我们将代码移动到了更合适的位置，代码看起来也更干净、更容易理解。

## 容易发现，简单修复，影响大

每次看到这个图案:

```
if(someObject.getSomeAttribute()) { 
    doSomethingWith(someObject); 
} else { 
    doSomethingElse(someObject); 
} 
```

很有可能会有改进设计的机会。

这种设计导致了重复、不必要的耦合和混乱的代码。

识别一些潜在的概念，并把它转移到适当的对象中，会让你的代码更容易、更便宜、更容易编写和维护。

应用“告诉，不要问”原则解决现实世界糟糕设计的帖子[最早出现在](https://guifroes.com/real-world-tell-dont-ask/)[的 Gui Froes](https://guifroes.com) 上。