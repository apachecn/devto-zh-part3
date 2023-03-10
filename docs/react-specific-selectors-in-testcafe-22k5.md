# 在 TestCafe 中反应特定的选择器

> 原文：<https://dev.to/pluralsight/react-specific-selectors-in-testcafe-22k5>

TestCafe 选择器函数允许您使用 CSS 选择器在 DOM 中查找需要测试的元素。使用 CSS 选择器非常方便，许多其他测试库也使用相同的方法。当使用 TestCafe 测试 React 应用程序时，使用普通的 CSS 选择器肯定能完成任务，这是我通常采用的方法。但这不是唯一的选择。TestCafe 有 React 特有的选择器，允许你遍历组件层次结构，通过名称、属性值甚至组件的状态来选择组件。

[![](img/26c95389dfccadfe6917c130333d5d6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tNILFNGR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y89b2pok97uda76o4b2r.png)

假设我们有上面的组件树。我们可以使用 React 选择器来获取 ProductList 组件，如下所示:

[![](img/29f21f4eabaf4f33d2d02b7ba4f28380.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pMchGE2P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nsamw0e2yyj79n49x0sc.png)

ReactSelector 函数接受一个组件名，应该从根组件开始。我们上面的 ProductList 变量的可比 CSS 选择器是:

```
Selector('.app > .productList'); 
```

ProductList 需要是 App 组件的直接子组件。这可能会变得很烦人，因为在一个更复杂的 React 应用程序中，可能会找到许多级别的嵌套组件。幸运的是，TestCafe 想到了:

[![](img/ef510a84518d6f2adca547033ca29672.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wcpYmPeW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fvwu0ccomv9dj7aq51yy.png)

findReact 函数允许您搜索嵌套在另一个组件中的 React 组件。它非常类似于 Selector 对象上的 find 方法。

```
Selector('#component').find('h1'); 
```

## 带道具({ … })

按名称选择组件既快速又简单。但是在实际应用中，更精确的东西会更好。您可以在 CSS 选择器中使用更多的选择性，或者使用 ReactSelector 的 withProps 函数来查找具有特定属性的组件。

[![](img/7329e8ae8e5cad89f704fbe41dd29990.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FVVI4HTm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m4xyo7mxk8cze780am3y.png)

即使你的属性值是一个对象，withProps 也可以工作，匹配可以是部分匹配，也可以是精确匹配。

[![](img/edc399e6334c68d257969e8bd52cd36f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ze91nM02--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a7s90votkg0pv13ignro.png)

对 React 组件了解得越多，就越容易在 DOM 中找到它们。如果你愿意，你可以绕过所有的 CSS 选择器。

但是，您需要小心使用这种方法。如果您在您的选择器中进行精确的对象匹配，但是随后决定向您的组件中的对象添加一个新的道具，那么您也需要更新端到端测试。我个人不喜欢把我的测试代码和我的应用程序代码捆绑在一起，因为这只会让我在开发一个特性时需要考虑其他的事情。我只是建议小心不要过度精确匹配，特别是在复杂的对象上，以免自己头疼。

## getReact()

在一些测试场景中，您希望测试属性是否被正确更新，或者检查 state 的值。理想情况下，您可以使用像 react-testing-library 这样的工具来测试这些类型的交互。如果您更愿意在端到端测试中检查内部，那么 getReact 函数就是您所需要的。getReact 函数返回一个客户端函数，它使用组件属性和状态的对象进行解析。

[![](img/a9dd4517c72898971a6d45ccace062ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tcgz8fly--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xc2x4b6brbx8exez45tr.png) 
利用这些信息，您可以根据状态值或组件属性运行期望值:

[![](img/a27d5254fbe23f99a3b87c17c4d58c44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bK30CCPM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0vo3ndtba4cv9cirq9s2.png)

我仍然建议使用 Jest 测试来进行这些检查，但有时这并不总是最简单的解决方案，尤其是当您有很多复杂的设置时。您可以在 TestCafe 测试中使用 getState，并在 chrome headless 模式下运行它。这可能运行得足够快，让它感觉像一个笑话测试，这取决于你的应用程序。试试吧:)

## 总之

TestCafe 是一个创建端到端测试的优秀工具，并且真正改变了开发人员对待测试的方式。有智能测试动作和断言，自动等待元素出现，不需要 webdriver，以及完整的 TypeScript 支持。除了 React 特定的选择器，还有用于 Vue、AngularJS、Angular 和 Aurelia 的其他选择器库。我认为在 React 应用中使用 React 选择器有很多好处，你应该在编写下一个测试时尝试一下！

对 React 组件了解得越多，就越容易在 DOM 中找到它们。如果你愿意，你可以绕过所有的 CSS 选择器。然而，你需要小心使用这种方法。如果您在您的选择器中进行精确的对象匹配，但是随后决定向您的组件中的对象添加一个新的道具，那么您也需要更新端到端测试。我个人不喜欢把我的测试代码和我的应用程序代码捆绑在一起，因为这只会让我在开发一个特性时需要考虑其他的事情。我只是建议小心不要过度精确匹配，尤其是在复杂的对象上。

在推特上关注我😀