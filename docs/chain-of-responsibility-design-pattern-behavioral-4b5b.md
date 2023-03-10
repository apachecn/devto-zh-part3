# 责任链设计模式[行为]

> 原文：<https://dev.to/itscoderslife/chain-of-responsibility-design-pattern-behavioral-4b5b>

**目标**:主要目标是将事件或请求的发送者从潜在接收者中分离出来。

接收对象形成一个链。基本上，它们形成一个链表，这样每个对象都可以引用下一个对象。请求通过这个链传递。每个接收对象可以决定是否处理请求。

如果一个对象不处理请求，它会将请求转发给链中的下一个处理程序。请求会一直传递，直到其中一个响应对象处理它。

如果没有一个对象处理请求，它将到达链的末端，什么也不会发生。

有时您可能需要转发请求，即使它是由响应者之一处理的。*如果没有响应者获得请求*的所有权，会发生什么？请求在链中不断传递，直到到达链的末端。

> 责任链将潜在的请求处理者组织成一个序列，并将响应者与调用者分离开来。请求会一直传递，直到其中一个响应者处理它，或者到达链的末端。

*示例流*:客户端发出请求，该请求到达链中的第一个响应者对象。这个对象不处理请求，所以它把请求转发给下一个响应者。第二个响应者也不处理请求。请求在响应者链中进一步传播。现在，第三个响应者对象决定处理请求。请求传播到此为止。下一个响应者不会被通知该请求。

在 iOS 和 macOS 中，责任链设计模式是响应者链的核心。

*   这种模式将请求处理器组织成一个链。
*   响应者可能会也可能不会处理该请求。
*   每个响应者可以处理请求，将请求转发给下一个响应者，或者在处理完请求后转发请求。
*   请求通过链传播，直到到达链中的最后一个响应者。

它是一种广泛使用的模式，可以解决需要在按顺序组织的对象之间传递请求的各种问题。

缺点:向链中的其他对象或调用者公开链中任何响应者的详细信息。