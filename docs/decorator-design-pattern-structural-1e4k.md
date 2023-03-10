# 装饰设计模式[结构]

> 原文：<https://dev.to/itscoderslife/decorator-design-pattern-structural-1e4k>

装饰设计模式允许向现有类型添加新的行为。我们可以扩展类型的功能，而不需要修改它的实现，这是子类化的一种灵活的替代方法。

装饰器可以通过包装要增强的对象来实现。扩展提供了向类型添加额外方法的能力，而不必取消或更改它们的源代码。

> 装饰设计模式给对象增加了新的职责，而不需要修改用来创建它们的代码类型

示例:

基本颜色类允许您用 RGB 和 HSB 创建颜色。如果我必须使用十六进制值创建一种颜色呢？通常我们子类化。我们不需要子类化，而是需要扩展相同的颜色类，并添加一个函数来创建十六进制值的颜色。

另一个例子是，当在网上订购食物时，一个典型的汉堡或赛百味三明治的价格是 x，然后用户还可以选择添加额外的奶酪或牛排。有时，供应商会提供向购物车添加饮料的选项。所有这些额外的费用将会增加。最后的成本是通过调用装饰者将他们的成本添加到购物车的基本商品上来计算的。

常见的误用:意外替换某个类型的现有功能。另一个误用是，我们可能通过用不相关的行为来修饰一个类型来改变它的原始目的。