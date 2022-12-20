# 什么是函数？

> 原文：<https://dev.to/codetips/what-is-a-function-2lho>

> 函数是预定义的代码块，可以执行它来完成特定的任务

[![What is a function?](img/2695d0a2cc1f5a520a252b0856ec9eea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l64700Ui--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/03/function_cooking.jpg)

你是否曾经完成一项任务，并希望有一种方法来自动完成它，这样你就再也不用做了？我们在编程中也有同样的问题。

每当我们遇到这样的任务时，我们可以写一次函数，让它根据我们的需要执行多次。

如果你有一个最喜欢的食谱，最初是为三个人准备的，但你经常有不同数量的客人，每次都必须手动计算出正确的配料量，该怎么办？

您必须执行的计算是:

*   将原始配料量除以三(得到每人所需的量)。
*   取人均量，乘以来的客人数。

```
getIngredientAmount(amount, people) {
    (amount / 3) * people
} 
```

让我们把上面的代码片段分解一下，让它更容易理解(我们将在后面进行更详细的讨论，所以如果你不理解，就坚持下去):

*   `getIngredientAmount`是函数名。
*   函数名后面的括号(`()`)表示我们可以传递给函数的内容。在这个例子中，我们传入了`amount`和`people`
*   花括号(`{}`)代表函数体。这是所有重复性工作发生的地方。

让我们更进一步，使用我们刚刚创建的新函数。想象一下，今晚我们有七个人要来吃饭:

```
getIngredientAmount(300, 7) 
```

这些值(`300`和`7`)然后被传递给`getIngredientAmount`函数，该函数使用这些值代替`amount`和`people`，类似于变量名/值的工作方式。该函数将运行以下计算:

`(300 / 3) * 7)`

现在我们已经写了这个函数，我们可以想用多少次就用多少次。

```
//-- Get recipe amounts for 5 people --//

// original recipe requires 4 parsnips
getIngredientAmount(4, 5)

// 20 grams of sugar
getIngredientAmount(20, 5)

// and 12 eggs
getIngredientAmount(12, 5) 
```

如果我们已经写了 100 个这样的计算，然后我们突然意识到原来的食谱实际上是为四个人写的！？

那很简单。我们只是更新功能。

```
getIngredientAmount(amount, people) {
    (amount / 4) * people
} 
```

现在任何使用这个函数的东西也将使用更新的需求。

这只是一个非常简单的函数介绍。我们可以用它们做更复杂的事情，但是我们将在以后的文章中讨论。