# 可选链接:它是什么，现在如何将它添加到您的 JavaScript 应用程序中？

> 原文：<https://dev.to/nimmo/optional-chaining-what-is-it-and-how-can-you-add-it-to-your-javascript-application-right-now-37ie>

这篇文章假设你已经用 Babel(7+版)移植了你的 JS 应用程序。如果你不是，那么这个特性可能不会说服你把它加入到你的构建过程中，但是它仍然是一个值得注意的语言特性。

您以前已经看到过这些错误，隐藏在您的日志中，在您的自动化测试读数中，在您的控制台中，在您的 devtools 中。

您花时间跟踪错误，并找到有问题的函数:

```
const someFunction =
  someArray => 
    someArray.map(someOtherFunction); 
```

Enter fullscreen mode Exit fullscreen mode

您甚至要花更多的时间来查看调用这个函数的代码。有时候数组可能真的是未定义的。在这种情况下，您决定由`someFunction`负责处理。你更新了你的代码，并留下了有用的评论，这样就不会有人浪费时间去想你为什么会这样:

```
const someFunction =
  (someArray) => {
    // Sometimes this is undefined: See [link to bug report]
    if (someArray === undefined) {
      return [];
    }

    return someArray.map(someOtherFunction);
  } 
```

Enter fullscreen mode Exit fullscreen mode

这个管用。但是，你有点喜欢原始例子中的隐式返回。函数中的单个表达式让你感觉更舒服。其他任何东西都不可能溜进去并造成问题。我喜欢你的想法。

您再次尝试，这次使用一个表达式，使用默认值:

```
const someFunction = 
  (someArray = []) => 
    // Sometimes this is undefined: See [link to bug report]
    someArray.map(someOtherFunction); 
```

Enter fullscreen mode Exit fullscreen mode

这个管用。但是，现在你的有用的评论有点奇怪。会不会有人认为这个函数的*输出*是未定义的，并意外地在其他地方说明了这种可能性，尽管这总是会返回一个数组？你想象一下你可能造成的混乱，以及由此给你的公司带来的累积(假设的)成本。

你可以让你的评论更清晰，但是你想用 JavaScript 解决这个问题，而不是枯燥的文字。

你可以接受三进制，但这意味着你必须多打一次字。让我们来看一个新的选择:

## 输入`optional chaining`

通过可选的链接，您有了一个新的操作符:`?.`

您可以对任何您认为可能未定义的内容使用`?.`,这可以避免您在 JS 中经常看到的最常见和最令人沮丧的问题。比如:

```
const withoutOptionalChaining =
  something
    && something.someOtherThing
    && something.someOtherThing.yetAnotherThing

const withOptionalChaining =
  something
    ?.someOtherThing
    ?.yetAnotherThing 
```

Enter fullscreen mode Exit fullscreen mode

重要的是要明白，如果`someOtherThing`或`yetAnotherThing`是`undefined`，那么`withoutOptionalChaining`的例子将是`false`，而`withOptionalChaining`的例子将是`undefined`。

如你所知，如果你写 JS 已经超过一天了。但是，如果那不重要呢？

```
const someValue = 
  someObject.someFunction?.() // returns `undefined` rather than a runtime error if `someFunction` is undefined! 
```

Enter fullscreen mode Exit fullscreen mode

## 我在。但是，怎么做呢？

幸运的是，这里有一个巴别塔插件:[@ Babel/plugin-proposal-optional-chaining](https://www.npmjs.com/package/@babel/plugin-proposal-optional-chaining)

用`npm`安装那个插件，然后[通过你选择的配置选项把它添加到你的巴别塔配置](https://babeljs.io/docs/en/plugins)中。

根据您的 Babel 配置的其余部分，您可能还会发现您最终会得到一个关于`regenerator runtime`没有被定义的错误。如果是这样，你可能还需要添加[@ babel/plugin-transform-runtime](https://babeljs.io/docs/en/babel-plugin-transform-runtime)，并像这样配置它:

```
['@babel/plugin-transform-runtime',
  {
    regenerator: true,        
  },
] 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在使用 ESlint，你会发现它对这个新的运营商不太满意。您还需要将 [babel-eslint](https://github.com/babel/babel-eslint) 插件添加到您的 eslint 配置中。

仅此而已。现在，您应该能够在应用程序中尽可能多地使用可选链接了。

让我们再看一下原始代码:

```
const someFunction =
  someArray => 
    someArray 
      // Sometimes this is undefined: See [link to bug report]
      ?.map(someOtherFunction)
      || []; 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们解决问题的另一个选择。你总是想这样吗？绝对不会:有时候你可能确实想要一个运行时错误。但是在其余的时间里，可选的链接是对工具箱的一个很好的补充。

## 免责声明

可选链接目前处于提案流程的第一阶段，因此您是否愿意现在就将其纳入取决于您。