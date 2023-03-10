# Nashorn JavaScript 引擎

> 原文：<https://dev.to/k4ml/nashorn-javascript-engine-3ja7>

几天前，在查看这个 [SIP 服务器](https://github.com/fonoster/routr)时，我注意到它是使用一种叫做 [Nashorn](https://en.wikipedia.org/wiki/Nashorn_(JavaScript_engine)) 的东西来执行的，而不是更流行的 JavaScript 运行时节点。

所以 Nashorn 是 Java 中的 JS 引擎，是 Mozilla Rhino 的继承者。坏消息是，在最新的 Java 中，它已经被弃用，并且没有提供替代方案。

当被问及 Nashorn 被 Java 弃用后的计划时，Routr 开发者说他考虑了两个选择

> GraalVM 取代了 Nashorn，成为其可行的替代品。在这种情况下，我将非常乐意移植该项目。*   Nasxiong died without a viable alternative. In this case, I have no choice but to rewrite the project in Java.