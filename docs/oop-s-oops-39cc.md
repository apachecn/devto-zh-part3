# OOP + S = OOPS

> 原文：<https://dev.to/cocoroutine/oop-s-oops-39cc>

## OOP

当我们第一次开始使用 OOP 时，它是关于对事物建模。更现代的说法是 OOP 是关于这些对象之间传递的消息。但是我画的消息图越多，我就越意识到 OOP 就像一个隐藏对象游戏。你总是在发现新的可以被赋予生命的事物。

### OOP 对手

一些 OOP 的讨厌者，比如 Youtube 的 Brian Will，说 OOP 让你对现实生活中不存在的对象建模。他们是你的“管理者”、“建设者”和“协调者”。是的，这些在现实世界中是没有意义的，但是有什么选择呢？长长的令人困惑的代码行？嵌入到代码中的文档会说谎吗？一堆蜘蛛模块和复杂的 api？

OOP 的另一个可行的解决方案是纯粹的函数式编程，但是这也有它自己的问题。

我承认，当我描述大图(阶级等级)时，它会变得模糊。全球范围是混乱的。然而，当我查看单个对象时，代码很清晰，几乎不需要文档。局部范围是干净的。这就是 UML 图可以提供帮助的地方。

## 固体中的 S

S 代表单一责任。一个方法、类或模块应该只负责一件事。一个方法、类或模块应该只有一个改变的理由。那么为什么实施起来这么难呢？

### 年代意味着斗争

不仅仅是你。单一责任规则完全是武断的。它随着问题空间的变化而变化。新手和经验丰富的程序员一样，仍然有问题。有了经验就容易多了。

### S 的意思是螺旋形

如果你严格遵守规则，你可能会得到只有一个函数的类。那么你会有太多的对象，它们之间的消息传递很混乱。你怎么知道什么应该是另一个对象，什么不应该？

这不是一个万能的答案。我会说倾向于拥有更多而不是更少的对象。除非你能有把握地回答这个对象的需求不会改变。如果你发现一个对象没有用，你总是可以合并功能。

然而，相反的情况并不总是正确的，这可能会导致螺旋式的变化。

### S 表示简单的统治

POODR 的 Sandi Metz 提出了一个简单的规则。方法应该最多 4 行，类应该最多 100 个。这是使用标准的 80 字符宽度。试试看。

4 行可能看起来限制太多，但实际上已经很多了。如果你有困难，那么你可以分开你的方法。如果它仍然太长，一个隐藏的物体可能正在等待被发现。

## OOP 真正的意思是

OOP 是关于保护你的代码不受变化的产品需求的影响。你的物品越小，你就越能防御。遵守单一责任规则对此有所帮助。

OOP 是关于 80/20 规则的。80%的收益应该来自 20%的努力。OOP 可以实现这一点。

## 清理代码

编写干净的代码并不容易。我仍在重构我的代码，你也应该如此。继续寻宝，找到那些隐藏的物品！