# javascript 中三元运算符和布尔赋值的研究

> 原文：<https://dev.to/duranenmanuel/a-look-into-ternary-operators-and-boolean-assignations-in-javascript-48fk>

嘿，未来的开发者们，欢迎你们，在这篇文章中，我将通过教你们如何使用三元运算符来帮助你们改进编码技术，我还将向你们解释为什么它是好的，以及什么时候你们应该和不应该使用它，所以让我们开始吧。

## 三元运算符

我知道我知道，“三元运算符”听起来花哨复杂，但相信我，这次这个名字很有意义，一点也不难理解，让我们花点时间来分析一下。

它被称为*三元*运算符，因为它由*的三个*参数组成，第一个是比较/条件参数，第二个是当条件参数评估为*真*时会发生什么，第三个是当它评估为*假*时会发生什么，你可以把三元运算符想象成一种编写条件语句的简短方式，下面是如何实现它的:

```
Conditional_Argument ? True_Argument : False_Argument; 
```

很简单吧？，现在举一个更现实的例子:

```
1 + 1 === 2 ? 'Correct' : 'Incorrect'; 
```

在上面的例子中，我们正在评估超级复杂的表达式 1 + 1 === 2，如果这是真的(当然是真的)，它将返回字符串`Correct`，如果它是假的，它将返回`Incorrect`，简单，现在让我告诉你什么时候它真的有用。

给`age`一个数字，假设我们有一个变量`canDrive`，它需要根据一个条件来定义(在现实世界的应用程序中，这种情况经常发生):

```
let canDrive;

if (age >= 18) {
    canDrive = 'Yes';
} else {
    canDrive = 'No';
}

console.log(canDrive); 
```

一个非常简单的条件，但仍然感觉是多余的，我们在 if 语句中声明一个变量，我们基于 if 条件定义它的值，所以我们对 *if* 和 *else* 的情况重复它，嗯…一定有更好的方法，现在让我们看看三元运算符做同样的事情:

```
const canDrive = age >= 18 ? 'Yes' : 'No'; // parenthesis are optional
console.log(canDrive); 
```

等等，什么？，一线？，神圣的 CSS 表…

让我解释一下为什么这么好，我至少能马上想到 3 件事:

1.  在第一个使用 *if* 语句的例子中，我们必须使用 *let* 声明`canDrive`，因为我们需要*console . log*if 的 *if 的* *块范围*(不知道*块范围*是什么意思？，阅读[这篇文章](https://enmascript.com/articles/2018/07/06/const-let-and-var)在那里我解释得更好)，在三元运算符的情况下，我们能够使用 *const* ，因为变量不受任何块的限制，这意味着我们有*更多的灵活性*。
2.  *少一些多余的*，再看一遍 if 的语句案例，检查一下你看到多少次`canDrive`变量被命名？，3 倍对吗？，现在看看三进制运算符的实现，只要一次…
3.  *更少的行，更容易更快的阅读*。

在上面的解释中，我没有提到三元运算符的实现更简洁(最重要的是，它让你第一次使用它时感觉很糟糕)。

**建议:**当你习惯于用三元运算符编写代码时，很容易写出糟糕的代码，因为“它的代码行更少”，为了避免犯这些错误，我建议你:

*   **当论点看起来令人困惑时，用括号来区分它们。**请注意，在我使用括号将*条件参数括起来之前，我们在示例中是如何分析的，*我这样做的原因是为了避免运算符`=`和`>=`靠得太近造成视觉混乱，因此，作为一般规则，如果您使用的运算符使语句读起来不舒服，那么就使用括号。
*   不要为了写三元运算符而过于复杂化。学习如何识别使用它是否是明智的选择，很多时候使用经典的 if 语句更好。

好了，现在，最后，我要把你的逻辑推进一步，我要试着帮助你理解编程是关于抽象的。如果我们之前看到的`canDrive`变量分配了一个布尔值(`true`或`false)`，而不是字符串`'Yes'`或`'No'`，会发生什么呢，我的意思是这样的:

```
const canDrive = age >= 18 ? true : false; 
```

看起来不错吧？，但是等等…现在看看这个:

```
const canDrive = age >= 18; 
```

这难道不会有同样的结果吗？，答案是肯定的，为什么？，因为被赋值的值是布尔求值的结果，根据条件的结果，只能以`true`或`false`结束，这是我很肯定你们大多数人已经知道的，但仍然想展示这个例子并说:是的，编程语言中有许多东西可以帮助我们编写更好、更简洁的代码， 但是最终最重要的是我们思考问题的能力，所以首先花时间分析它们，然后用你学到的东西和你的逻辑，我很确定你会写出很棒的代码。

我希望你喜欢这篇文章，如果是这样的话，请分享它，并在 [twitter](https://twitter.com/duranenmanuel) 或 facebook 上发表评论。如果你想贡献任何与这个主题相关的东西，谢谢大家，下一篇再见。