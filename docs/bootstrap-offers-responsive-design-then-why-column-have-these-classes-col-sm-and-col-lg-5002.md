# Bootstrap 提供响应式设计，那么为什么色谱柱有这些类别:col-sm-*和 col-lg-*

> 原文：<https://dev.to/jkimquickdev/bootstrap-offers-responsive-design-then-why-column-have-these-classes-col-sm-and-col-lg-5002>

在学习 bootstrap 时，我总是想知道为什么我在定义列时会有不同版本的 CSS 类。

我翻了一堆 bootstrap 文档，也看不懂。

## 疑问变得混乱

令人困惑的是，BootStrap 3 是一个移动优先响应系统，并且在 BootStrap 文档的该部分中未能解释这如何影响 col-xx-n 层次结构。这让您想知道如果您为较大的设备选择一个值，在较小的设备上会发生什么，并让您想知道是否有必要指定多个值。(你没有)

如果您认为这些列从水平方向开始，那么您可以选择何时希望它们堆叠。

## 用例子理解

例如，如果您从列开始:A B C

您决定它们何时应该像这样堆叠:

A

B

C

**如果选择 col-lg，那么当宽度为< 1200px** 时，列将会堆叠。

如果选择 col-md，那么当宽度为< 992px 时，列将会堆叠。

如果选择 col-sm，那么当宽度为< 768px 时，列将会堆叠。

如果您选择 col-xs，那么这些列将永远不会堆叠。

### 另一方面，如果你认为柱子从堆叠开始，那么你可以选择它们在什么点变成水平的:

如果选择 col-sm，那么当宽度为> = 768px 时，列将变为水平。

**如果选择 col-md，那么当宽度为> = 992px 时，列将变为水平。**

**如果选择 col-lg，那么当宽度为> = 1200px 时，列将变为水平。**

### 参考文献

[https://stack overflow . com/questions/19865158/the-difference-in-col-LG-col-MD-and-col-sm-in-bootstrap](https://stackoverflow.com/questions/19865158/what-is-the-difference-among-col-lg-col-md-and-col-sm-in-bootstrap)

## 喜欢和追随

如果有人喜欢你的帖子，这真的会鼓励你多写，所以如果你喜欢它，一定要喜欢它！

跟我来，继续获得像这样的新的令人兴奋的东西！

> [金约翰](http://jsongrid.com/json-grid)
> T3】Json 格子*<=用爱发展！*