# 将操作附加到 Spring 中的回滚

> 原文：<https://dev.to/virtualmackem/attaching-actions-to-rollbacks-in-spring-g1g>

我们在一个 Spring Boot 应用程序中遇到了一个问题，其中一些文件是在数据库事务期间创建的，如果事务回滚，它们需要被删除。这是我的解决方案...

当一个文件被创建时，我们发布一个包含文件名的事件:

```
applicationEventPublisher.publishEvent(new FileCreatedEvent(this, filename)); 
```

我们还有另一个监听事件的组件:

```
@TransactionalEventListener(phase = TransactionPhase.AFTER_ROLLBACK)
public void handleRollback(FileCreatedEvent event) {
    fileDeleter.delete(event.getFilename()); 
} 
```

正如您所看到的，Spring Framework 提供了一个发布/订阅事件管理特性，您可以使用[ApplicationEventPublisher](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationEventPublisher.html)发布事件(可以很容易地自动连接)。然后，您可以使用一个[@ transactionaleventlistener](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/event/TransactionalEventListener.html)注释 [^1](#1) 来订阅该事件，通过将注释上的 phase 参数设置为 [AFTER_ROLLBACK](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationEventPublisher.html) ，可以将该注释定制为仅在回滚后触发(如图所示)。

至关重要的是，在事务解决之前，事件不会传递给侦听器。这使得事务的逻辑更加简单。此外，这适用于附加到同一事务的多个同时发生的事件，这意味着您可以为每个文件触发离散的事件，并且每个事件将在事务完成时交付。

这种模式的另一个潜在用途是通过为新条目触发一个事件来防止误导日志/审计条目，但是只有当事务成功提交时才记录该事件。这样，您就不会看到日志消息说“在数据库中创建了新实体”，除非新实体确实被创建了。

 如果你使用一个简单的 [@EventListener](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/event/EventListener.html) 进行订阅，这种模式在没有交易的情况下也能工作。在这种情况下，侦听器会立即处理该事件，而不是等到事务结束。