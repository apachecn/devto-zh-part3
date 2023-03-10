# 初级开发学习:包装类

> 原文：<https://dev.to/ryhenness/junior-dev-learning-wrapper-classes-3l8d>

# 概述

*   什么是包装类？
*   基本包装示例
*   真实世界的包装示例

* * *

# 什么是包装类？

包装类的主要思想是模仿另一个类的功能，同时隐藏被模仿类的复杂性。包装器是一个类，它将另一个类(被包装的类)的实例作为其构造函数中的一个参数，使它成为被包装的类的一个基本类。这意味着包装器可以访问被包装的类的功能，并且能够在这个被包装的类之上公开所需的特性/构建实用程序组件。在本帖中，我们将使用 C#来演示，但这种模式在所有面向对象的语言中都是可行的。

* * *

# 裸机包装示例

好的，我们知道包装器应该“包装”另一个类。但是这个结构实际上是什么样子的呢？这很简单，包装器将包含一个私有类变量，它是被包装类的类型。包装器的构造函数将接受包装类的一个实例，并将私有类变量设置为这个实例。这允许我们添加到包装的类中，而不需要实际修改它。在这个例子中，有一个类`CoolPerson`，它有`FirstName`和`LastName`类变量。这个`CoolPerson`类有两个方法:一个说它的名，另一个说它的姓。如果我们想要一个能同时说出两个名字的方法呢？这就是`CoolPersonWrapper`类发挥作用的地方。这个`CoolPersonWrapper`将用一个`FullName`类变量和一个表示全名的方法添加到`CoolPerson`中。

在这个块中，我们看到主程序实例化了一个`CoolPerson`，并将这个`CoolPerson`包装在一个`CoolPersonWrapper`中。然后包装器继续用一个方法说出它的全名。

```
namespace WrapperExample
{
    class Program
    {
        static void Main(string[] args)
        {
            CoolPerson someCoolPerson = new CoolPerson("Cool", "Dude");
            CoolPersonWrapper someCoolPersonWrapper = new CoolPersonWrapper(someCoolPerson);
            someCoolPersonWrapper.SayFullName();
            Console.ReadKey();
        } 
```

在这个模块中，我们看到了`CoolPerson`的定义，它有两种不同的方法来表达它的名和姓。

```
 public class CoolPerson
        {
            public string FirstName { get; set; }
            public string LastName { get; set; }

            public CoolPerson(string firstName, string lastName)
            {
                this.FirstName = firstName;
                this.LastName = lastName;
            }

            public void SayFirstName()
            {
                Console.WriteLine("My first name is " + this.FirstName);
            }

            public void SayLastName()
            {
                Console.WriteLine("My last name is " + this.LastName);
            }
        } 
```

在这个块中，我们看到`CoolPersonWrapper`在构造函数中取了一个`CoolPerson`并使用这个`CoolPerson`来设置`FullName`变量，这个变量在单个`SayFullName()`方法中使用，这个方法是我们在包装类的顶部添加的功能。我们还可以看到`SayFirstName()`和`SayLastName()`都是来自包装类的方法，我们用公共方法公开它们。

```
 public class CoolPersonWrapper
        {
            private CoolPerson CoolPerson { get; set; }
            private string FullName { get; set; }

            public CoolPersonWrapper(CoolPerson coolPerson)
            {
                this.CoolPerson = coolPerson;
                this.FullName = coolPerson.FirstName + " " + coolPerson.LastName;
            }

            public void SayFullName()
            {
                Console.WriteLine("My name is " + this.FullName);
            }

            public void SayFirstName()
            {
                this.CoolPerson.SayFirstName();
            }

            public void SayLastName()
            {
                this.CoolPerson.SayLastName();
            }
        }
    }
} 
```

控制台输出如下:

```
My name is Cool Dude 
```

* * *

# 真实世界包装器示例

为什么我们不把`SayFullName()`方法添加到`CoolPerson`类中呢？嗯，那也可以，但是如果`CoolPerson`类是我们无权修改的第三方库的一部分呢？改变第三方类的功能是需要包装的地方。

作为一个例子，我们可以讨论处理 COM 对象的情况。在开发 Excel 或 Word 插件时，开发人员将使用需要一些手动内存管理的 COM 对象。开发人员需要使用一个传递 COM 对象的`Marshal.ReleaseComObject()`方法调用来释放这些对象。除此之外，开发人员必须用 try/finally 包装使用 COM 对象的代码块，并在 finally 块中调用 release 方法。这要求开发人员也在 try/finally 之外声明 COM 对象。这会变得很难看。下面是一个例子:

```
 public void SetApplication()
        {
            WordInterop.Application application = null; // COM Object
            try
            {
                application = new WordInterop.Application();
            }
            finally
            {
                if (application != null)
                {
                    application.Quit();
                    Marshal.ReleaseComObject(application);
                }
            }
        } 
```

想象一下，当添加更多的 COM 对象时，它会变成什么样子！我们能做些什么使这个更干净？我们知道 C#有一个`IDisposable`对象接口，它允许我们为实现这个接口的对象使用一个`using`块，当这个块完成时，它将正确地处理这个对象。我们可以使用一个包装类来做这件事！我们所做的是将`WordInterop.Application`包装在一个新的类中，`WordApplicationWrapper`。在这个新的包装类中，我们还将实现`IDisposable`。为了满足`IDisposble`的需求，我们将实现一个在`using`结束时被包装的对象被释放时被调用的方法。这个方法就是我们能够调用`Marshal.ReleaseComObject()`的地方。这允许我们现在使用 using 块代替这个 try/catch，并在外面声明:

```
 public void SetApplication()
        {
            using (var application = new WordApplicationWrapper(new WordInterop.Application()))
            {
                application.Quit();
                // When we reach the end of the using block, the wrapped object
                // will be disposed of.
            }
        } 
```

干净多了，包装纸为我们提供了良好的服务。:)

* * *

你可以在这里查看我的 GitHub 教程。

如果这篇文章对你有帮助，你可能也会喜欢:

*   [Node.js + Express 服务器设置](https://dev.to/ryhenness/nodejs--express-server-setup-6ch)
*   [带有 Node.js 的外部路由](https://dev.to/ryhenness/external-routes-with-nodejs-1ni)
*   [制作 Node.js 推特机器人](https://dev.to/ryhenness/nodejs-twitter-bot-bogus-definition-dmm)