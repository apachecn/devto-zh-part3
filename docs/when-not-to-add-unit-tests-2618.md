# 何时不添加单元测试

> 原文：<https://dev.to/megazear7/when-not-to-add-unit-tests-2618>

重构编写得很差的代码，没有什么抽象，没有分离关注点，可以大大降低缺陷的风险，并加快未来的开发。在这个小规模的重新设计过程中添加单元测试，可以在最初的实现中获得单元测试的所有好处。

在没有任何重构的情况下，将单元测试添加到编写糟糕的代码中，这一点都没有实现。相反，它成为重构和改进代码的障碍。如果一个方法或类服务于多种目的，并且没有抽象出潜在的复杂性，那么它就会成为缺陷的入口点，并且经常会增加添加或提炼特性的成本。对这样的方法或类进行单元测试使得重构和消除这些缺陷变得更加困难。

最后，通过此类代码的单元测试并不能保证代码正常运行。通过测试反而会产生假阳性。如果代码对它应该做什么没有明确的定义，那么一个测试说它正在做它应该做的事情是没有帮助的。

我可以测试锤子挖洞的能力，然后用锤子拧螺丝。在这种情况下，我的测试给了我一个错误的信心，让我对锤子的工作能力产生了怀疑。同时，锤子的预期用途被遗漏了。这种情况的真正答案是使用螺丝刀。这类似于重构代码。然后测试螺丝刀拧入螺钉的能力。这类似于单元测试。

查看我的博客,了解更多我对技术和其他各种话题的思考。