# WTF 是动作委托？

> 原文：<https://dev.to/jonhilt/wtf-is-an-action-delegate-9j6>

在上一篇文章中，我们[探讨了什么是 lambda](https://dev.to/jonhilt/wtf-is-a-lambda-2dg2)，以及奇怪的`x=>x`语法是怎么回事。

现在我们知道了*是什么*，你可能想知道*在哪里可以使用*它们，以及如何编写自己的代码来使用 lambda。

答案是代表们！

委托充当方法的一种占位符。

我们举个例子。

```
static void SaveToDatabase(Person person)
{
    try
    {
        // save here
    }
    catch (Exception)
    {
        Console.WriteLine("could not save");
    }
} 
```

这个方法理论上可以保存一个 person 对象。如果出现问题，我们会捕捉异常，并在控制台上记录一条错误消息。

## 即插即用行为

现在想象一下，我们将从不同的地方调用这个`SaveToDatabase`方法，并希望能够根据具体情况改变我们记录该消息的方式。

例如，有时我们可能会选择发送电子邮件，而不是写信到控制台。

解决这个问题的一个方法是让`SaveToDatabase`有可能接受一个方法来记录实际的错误。然后我们可以选择是传入一个写控制台的方法还是一个发送电子邮件的方法。

```
static void SaveToDatabase(Person person, Action onError)
{
    try
    {
        // save here
    }
    catch (Exception)
    {
        onError();
    }
} 
```

`SaveToDatabase`现在表示它期望接收一个方法(`onError`)。

这里的`Action`类型将我们的`onError`参数定义为一个委托，它接受零个参数并且不返回任何东西。

此时,`SaveToDatabase`不知道`onError`做了什么，它只知道它的形状(一个没有参数的方法，返回 void ),并且可以使用这个语法随时“调用”它...

```
onError(); 
```

这让我们在调用`SaveToDatabase`时可以传递不同的方法。

<center>
![](img/e08c2e836ed1363041764d857a7ffbb9.png)
</center>

像这样...

```
static void Main(string[] args)
{
    var person = new Person { Name = "bob" };
    SaveToDatabase(person, WriteErrorToConsole);
}

private static void WriteErrorToConsole()
{
    Console.WriteLine("Could not save");
} 
```

这里我们创建了一个方法，它不接受任何参数，返回 void(满足对`onError`的要求)，并将对它的引用传递给`SaveToDatabase`。

注意我们没有在`Main`方法中调用`WriteErrorToConsole`。

我们决不会这样做...

```
WriteErrorToConsole() 
```

因为这将立即调用该方法并向控制台写入一条消息。

我们想要的是将该方法传递给`SaveToDatabase`，它将在自己选择的时间调用该方法。

还记得我们在上一篇文章中发现我们可以采用像`WriteErrorToConsole`这样的方法，并用 lambdas“内联”它吗？

我们可以在这里做一些非常相似的事情...

```
static void Main(string[] args)
{
    var person = new Person { Name = "bob" };
    SaveToDatabase(person, ()=> Console.WriteLine("Could not save"));
} 
```

我们不能使用`x=>x`，因为`onError`不期望任何参数。相反，我们使用空括号来达到同样的目的，但是对于没有参数的操作。

## 我想说点别的

如果我们不想将“无法保存”写入控制台，而是提供一个更具体的消息(根据实际错误而有所不同)，该怎么办？

为此，我们需要让我们的`onError`动作接受某种错误消息...

```
catch (Exception ex)
{
    onError(ex.Message);
} 
```

无论传入哪个方法(发送电子邮件的方法，还是写入控制台的方法)都可以利用这个消息字符串，并随心所欲地处理它。

为了实现这一点，我们需要稍微调整一下我们对`onError`动作的声明。

```
static void SaveToDatabase(Person person, Action<string> onError)
{
    try
    {
        // save here
    }
    catch (Exception ex)
    {
        onError(ex.Message);
    }
} 
```

现在我们使用`Action<string>`,这意味着我们期待一个方法，它接受一个字符串参数并返回 void。

我们的`Main`方法可以接受那个参数，并在写入控制台时使用它。

```
static void Main(string[] args)
{
    var person = new Person { Name = "bob" };
    SaveToDatabase(person,
        message => Console.WriteLine($"Could not save, error: {message}"));
} 
```

## 到处都是代表代表

正如您所看到的，委托非常强大，可以很好地使用相同的函数来做略有不同的事情，这取决于您传递的委托。

这里我们坚持使用`Action`委托，但是你也可以使用一个叫做`Func`的东西。

下次会有更多。