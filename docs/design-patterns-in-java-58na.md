# Java 中的设计模式

> 原文：<https://dev.to/awwsmm/design-patterns-in-java-58na>

我认为写一系列关于 Java 中不同设计模式的博客文章会很有趣。所以我做的第一件事就是上网，试着构建一个通用设计模式的列表。作为一个 A 型人，我想为现存的每一种设计模式写一篇文章。

> 下面是模式列表(我从 Google 上的一些热门搜索中构建的)，它为 Java 程序编号了超过 100 种不同的设计模式。

所以我想讨论一下设计模式的一般情况。 *100* 是否有太多不同的设计模式？这是 Java 表现力的证据吗？或者这是语言的一个失败，许多不同的用例需要被明确地映射出来？这是不是模板化代码太多了？

你有什么看法？

另外，你知道有哪些设计模式不在我的列表中吗？-a 型)

```
Java Design Patterns:

API Gateway Pattern [3]
Abstract Document Pattern [3]
Abstract Factory Pattern [1-5]
Action Pattern => Command Pattern
Acyclic Visitor Pattern [3]
Adapter Pattern [1-4]
Aggregator-Microservices Pattern [3]
Ambassador Pattern [3]
Async Method Invocation Pattern [3]

Balking Pattern [3]
Borg Pattern => Monostate Pattern
Bridge Pattern [1-4]
Builder Pattern [1-5]
Business Delegate Pattern [2-3]

Caching Pattern [3]
Callback Pattern [3]
Chain Of Responsibility Pattern [1-4]
Collection-Pipeline Pattern [3]
Command Pattern [1-4]
Command Query Responsibility Segregation (CQRS) Pattern [3]
CompletableFuture Pattern => Promise Pattern
Composite Pattern [1-4]
Composite Entity Pattern [2]
Converter Pattern [3]
Counting Semaphore Pattern => Semaphore Pattern
Criteria Pattern => Filter Pattern

Data Access Object (DAO) Pattern [2-4]
Data Bus Pattern [3]
Data Mapper Pattern [3]
Data Transfer Object Pattern [3]
Decorator Pattern [1-5]
Delegation Pattern => Proxy Pattern
Dependency Injection Pattern [3-4]
Dirty Flag Pattern [3]
Double-Checked Locking Pattern [3]
Double Dispatch Pattern [3]

Enterprise Integration Patterns: Aggregator [3]
Enterprise Integration Patterns: Message Channel [3]
Enterprise Integration Patterns: Publish / Subscribe [3]
Enterprise Integration Patterns: Splitter [3]
Enterprise Integration Patterns: Wire Tap [3]
Event Aggregator Pattern [3]
Event-Based Asynchronous Pattern [3]
Event-Driven Architecture Pattern [3]
Event Queue Pattern [3]
Event Sourcing Pattern [3]
Execute Around Pattern [3]
Extension Objects Pattern [3]

Facade Pattern [1-5]
Factory Pattern [1-2,4-5]
Factory Kit Pattern [3]
Factory Method Pattern [3]
Feature Flag Pattern => Feature Toggle Pattern
Feature Toggle Pattern [3]
Filter Pattern [2-3]
Fluent Interface Pattern [3]
Flux Pattern [3]
Flyweight Pattern [1-4]
Front Controller Pattern [2-3]

Guarded Suspension Pattern [3]

Half-Sync/Half-Async Pattern [3]
Handle/Body Pattern => Bridge Pattern
Hexagonal Architecture Pattern [3]

Intercepting Filter Pattern [2-3]
Interpreter Pattern [1-4]
Iterator Pattern [1-4]

Kit Pattern => Abstract Factory Pattern

Layers Pattern [3]
Lazy Loading Pattern [3]

Map-Reduce Pattern [3]
Marker Interface Pattern [3]
Master-Slave Pattern => Map-Reduce Pattern
Master-Worker Pattern => Map-Reduce Pattern
Mediator Pattern [1-4]
Memento Pattern [1-4]
Model-View-Controller (MVC) Pattern [2-4]
Model-View-Presenter (MVP) Pattern [3]
Module Pattern [3]
Monad Pattern [3]
Monostate Pattern [3]
Multiton Pattern [3]
Mute Pattern [3]
Mutex Pattern [3]
Mutual Exclusion Lock Binary Semaphore Pattern => Mutex Pattern

Naked Objects Pattern [3]
Null Object Pattern [2-3]

Objects For States Pattern => State Pattern
Object-Mother Pattern [3]
Object Pool Pattern [3]
Observer Pattern [1-4]

Page Object Pattern [3]
Partial Response Pattern [3]
Poison Pill Pattern [3]
Policy Pattern => Strategy Pattern
Private Class Data Pattern [3]
Producer-Consumer Pattern [3]
Promise Pattern [3]
Property Pattern [3]
Prototype Pattern [1-2,4-5]
Proxy Pattern [1-4]

Queue-Based Load Leveling Pattern [3]

Reactor Pattern [3]
Reader-Writer Lock Pattern [3]
Registry Pattern => Multiton Pattern
Repository Pattern [3]
Resource Acquisition is Initialization Pattern [3]
Retry Pattern [3]

Semaphore Pattern [3]
Servant Pattern [3]
Serverless Pattern [3]
Service Layer Pattern [3]
Service Locator Pattern [2-3]
Singleton Pattern [1-5]
Spatial Partition Pattern [3]
Specification Pattern => Filter Pattern
State Pattern [1-4]
Step Builder Pattern [3]
Strategy Pattern [1-4]
Surrogate Pattern => Proxy Pattern

Template Pattern [1-2]
Template Method Pattern [3-4]
Thread Pool Pattern [3]
Throttling Pattern [3]
Thread-Local Storage (TLS) Pattern [3]
Tolerant Reader Pattern [3]
Trampoline Pattern [3]
Transaction Pattern => Command Pattern
Transfer Object Pattern [2]
Twin Pattern [3]

Unit Of Work Pattern [3]

Value Object Pattern [3]
Virtual Constructor Pattern => Factory Method Pattern
Visitor Pattern [1-2,4]

Wrapper Pattern => Adapter Pattern / Decorator Pattern

[1] https://www.javatpoint.com/design-patterns-in-java
[2] https://www.tutorialspoint.com/design_pattern/
[3] https://github.com/iluwatar/java-design-patterns
[4] https://www.journaldev.com/1827/java-design-patterns-example-tutorial
[5] https://www.jmdoudoux.fr/java/dej/chap-design-patterns.htm#design-patterns-1 
```

Enter fullscreen mode Exit fullscreen mode