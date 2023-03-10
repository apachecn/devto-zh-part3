# Override 关键字

> 原文：<https://dev.to/flippedcoding/the-override-keyword-3cdh>

理解 web 开发中的基础知识总是有好处的。话虽如此，你对覆盖修改器了解多少？有时它很容易使用，不去想它，你会忘记它是什么。

override 关键字用于给出从基类继承的方法的新实现。如果你需要快速复习关于继承的知识，你可以在这里查看。基方法必须是虚拟的或抽象的才能被重写，并且继承的方法必须使用与基方法相同的名称。它们还必须具有相同的访问级别修饰符，以确保它们正常工作。因此，如果一个是公共的，另一个也必须是公共的。

override 关键字还确保从继承类派生的任何对象使用继承类的任何方法的实现，而不是基类实现。这里有一个小例子来说明这一点:

[![override.jpg](img/1a6ab0f4a496a514e3106be6ef67d48f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wgBP-Bew--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1547559242497/r1M8-Dsf4.jpeg)

重写方法不能通过使用新的、静态的或虚拟的修饰符来修改，所以就这样了。实际上，override 关键字简单易用。你只要注意什么时候需要用就行了。几乎任何时候你有一个带有抽象或者虚拟修饰符的基类方法，那么你就可以期望在继承它的时候覆盖它。

默认情况下，C#方法不是虚拟的或抽象的，所以您不必担心在您继承的每个方法上使用 override。这使得在一个基类中使用将被不同的派生类继承的多个方法变得更加容易。你只需要实现你虚拟或抽象的方法。

有时候以代码以外的形式来看这些东西是很好的。这可能是“啊哈”时刻到来之前的最后一小块了。我希望这能帮助你理解覆盖修改器是如何工作的！只是需要一些时间来记住你修改了哪些方法，但是一旦你记下了，你就可以开始了。

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)