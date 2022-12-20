# 使用 React 钩子更新模型属性的最好方法是什么

> 原文：<https://dev.to/nbrustein/what-is-the-best-way-to-update-a-property-on-a-model-using-react-hooks-ajc>

我正在从 angularjs 转换到 react，并且仍然在学习 react 背后的基本哲学。

我在尝试将输入元素绑定到模型的属性时遇到了一个问题。我首先尝试使用`useReducer`，它可以很好地更新模型。问题是，它不会触发组件的重新呈现，因为模型仍然是相同的模型，只是具有更新的属性。就 react 而言，什么都没有改变，所以不需要重新渲染。

我提出了两种不同的解决方案。第一个是在每次更新时克隆模型。在某些情况下，这很好，也许这是函数式编程的做事方式(我对函数式编程几乎和对 React 一样陌生)。但是总是到处克隆东西似乎是一种浪费。

第二个解决方案是使用发布在[https://medium . com/crowd botics/how-to-use-usereducer-in-react-hooks-for-performance-optimization-ecaf ca 9 e 7 BF 5](https://medium.com/crowdbotics/how-to-use-usereducer-in-react-hooks-for-performance-optimization-ecafca9e7bf5)的`forceUpdate`策略。如果我用一个定制的钩子把它包起来，我可以把它弄得很干净。但是我知道 forceUpdate 并不是真正推荐的，而且我对 react 还不够熟悉，不知道这是否是使用它的合理场合。

在 https://jsfiddle.net/nbrustein/odjwkbvs/31/，我已经把所有的事情都清楚地安排好了

那么，我应该在这里做什么？解决方案是:

1.  别担心，爱克隆人吧。这是目前所有酷的函数式程序员都在做的事情。
2.  使用 forceUpdate。这就是它的用途。
3.  其他我还没想到的。

提前感谢任何见解。