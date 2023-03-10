# 战略设计模式[行为]

> 原文：<https://dev.to/itscoderslife/strategy-design-pattern-behavioral-11je>

策略模式允许我们通过切换算法来改变类型的行为。

该模式通过为一系列算法定义一个公共接口来工作。每个算法都封装在自己的策略类型中。客户端只依赖于接口，所以它可以通过简单地切换具体的策略对象来改变算法。有一个上下文，它将客户机请求委托给它的策略。战略是一个协议；它为所有具体算法定义了一个公共接口。具体的策略类型实现特定的算法。客户端用 concretestrategy 对象实例化上下文。然后，可以通过对上下文应用不同的策略实例来更新算法。

1.  确定客户喜欢的行为。
2.  在接口中指定该算法的签名。
3.  将可选的实现细节隐藏在派生类中。
4.  算法的客户端将自己耦合到接口。

> 策略模式是关于将算法实现细节从使用它的类型中分离出来。这允许在运行时通过切换算法来改变对象的行为。

示例:

从一个地方旅行到另一个地方的交通方式是一个微不足道的例子，但很适合 IMHO。我可以选择自己的车或公共交通工具——公共汽车或出租车，也可以选择飞机。每当我想上下班的时候，我可以选择这些中的任何一个。

日志系统，有登录控制台、文件或内存等选项。用户可以在使用时选择策略。

因此，如果您需要实现需要互换使用的算法，应该使用该策略。该算法与使用它的类型是分离的。上下文只看到协议，这允许客户端在各种实现之间轻松切换。策略模式的另一个好处是，它允许我们添加新的算法，而无需修改使用它们的类。

策略模式非常简单，我无法提及任何陷阱。