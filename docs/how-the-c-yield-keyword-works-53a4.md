# C# yield 关键字的工作原理

> 原文：<https://dev.to/mhmd_azeez/how-the-c-yield-keyword-works-53a4>

[C# yield 关键字](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/yield)是一个有趣的关键字，因为当你使用它时，你可以返回一个`IEnumerable<T>`而不用显式指定任何具体的实现。

下面是一段代码:

```
static IEnumerable<int> GetNumbers()
{
    yield return 0;
    yield return 1;
    yield return 2;
}

static void Main(string[] args)
{
    var numbers = GetNumbers();
    foreach (var n in numbers)
        Console.WriteLine(n);
} 
```

这是输出:

```
0
1
2 
```

但是这是如何工作的呢？`GetNumbers`到底回报什么？让我们再深入一点:

```
static void Main(string[] args)
{
    var numbers = GetNumbers();
    Console.WriteLine(numbers.GetType().Name);
} 
```

`GetType()`返回一个对象的类型，`Name`给出该类型的名称。

我们会看到它输出`<GetNumbers>d__0`。奇怪吧？因为在 C#中，你不能在标识符中使用`<`或`>`。这是因为`<GetNumbers>d__0`实际上是编译器在编译时生成的一个类。它代表一个实现`GetNumbers()`的[状态机](https://medium.freecodecamp.org/state-machines-basics-of-computer-science-d42855debc66)。

为了更好地理解它，你必须看到反编译的 C#，这在 [SharpLab](https://sharplab.io/#v2:D4AQDABCCMDcCwAocVoBYGKQOwIYFsBTAZwAdcBjQiACUIBt6B7AdSYCd6ATJAbyQiCoAJlQB2AUP6Ihs1ADZUAZgA8AS2wAXAHwQA4oU0A5AK74ARoXbEAFAEpJciNKdOY0KGIhhMrue88IOEc/VA8QL2FfVwBfJBDZGEUQNAgAWVwNGxgwAG0AXQhcdgBzYgcZVxdQwQA3YohsM0trCABefUNTCytbO2iamABOGyae6wA6A00AFQBPUkJ7CaMCQn6EoTjKwW2YoA==) :
的帮助下相当容易

```
namespace HelloWorld
{
    internal class Program
    {
        [CompilerGenerated]
        private sealed class <GetNumbers>d__0 : IEnumerable<int>, IEnumerable, IEnumerator<int>, IDisposable, IEnumerator
        {
            private int <>1__state;

            private int <>2__current;

            private int <>l__initialThreadId;

            int IEnumerator<int>.Current
            {
                [DebuggerHidden]
                get
                {
                    return <>2__current;
                }
            }

            object IEnumerator.Current
            {
                [DebuggerHidden]
                get
                {
                    return <>2__current;
                }
            }

            [DebuggerHidden]
            public <GetNumbers>d__0(int <>1__state)
            {
                this.<>1__state = <>1__state;
                <>l__initialThreadId = Environment.CurrentManagedThreadId;
            }

            [DebuggerHidden]
            void IDisposable.Dispose()
            {
            }

            private bool MoveNext()
            {
                switch (<>1__state)
                {
                    default:
                        return false;
                    case 0:
                        <>1__state = -1;
                        <>2__current = 0;
                        <>1__state = 1;
                        return true;
                    case 1:
                        <>1__state = -1;
                        <>2__current = 1;
                        <>1__state = 2;
                        return true;
                    case 2:
                        <>1__state = -1;
                        <>2__current = 2;
                        <>1__state = 3;
                        return true;
                    case 3:
                        <>1__state = -1;
                        return false;
                }
            }

            bool IEnumerator.MoveNext()
            {
                //ILSpy generated this explicit interface implementation from .override directive in MoveNext
                return this.MoveNext();
            }

            [DebuggerHidden]
            void IEnumerator.Reset()
            {
                throw new NotSupportedException();
            }

            [DebuggerHidden]
            IEnumerator<int> IEnumerable<int>.GetEnumerator()
            {
                if (<>1__state == -2 && <>l__initialThreadId == Environment.CurrentManagedThreadId)
                {
                    <>1__state = 0;
                    return this;
                }
                return new <GetNumbers>d__0(0);
            }

            [DebuggerHidden]
            IEnumerator IEnumerable.GetEnumerator()
            {
                return System.Collections.Generic.IEnumerable<System.Int32>.GetEnumerator();
            }
        }

        [IteratorStateMachine(typeof(<GetNumbers>d__0))]
        private static IEnumerable<int> GetNumbers()
        {
            return new <GetNumbers>d__0(-2);
        }

        private static void Main(string[] args)
        {
            Console.WriteLine(GetNumbers().GetType().Name);
        }
    }
} 
```

如您所见，编译器生成了一个隐藏的`<GetNumbers>d__0`类，并且修改了`GetNumbers()`以返回该类的一个新实例。`<GetNumbers>d__0`最有趣的部分是`MoveNext()`方法，其中编译器将 GetNumbers()中的逻辑翻译成状态机。

[罗斯林](https://github.com/dotnet/roslyn)(C #编译器)为你生成大量代码，这种操作叫做降低。其他一些例子是当你使用 [`for each`](https://sharplab.io/#v2:D4AQDABCCMDcCwAocVoDoAyBLAdgRwWUhgFZCkcBDAWwFMBnAB0oGNaIAJWgG24HsA6nwBO3ACZIA3kgiyoAJlQB2GXOmI5m1ADYoAFggBZSrgAUMMAG0AuhErCA5vQCUqrRHXv3AN3sQcAK7UAEa0wvQQALwQAKKBdMKUwdy0aABKlDgOtKbQADQQ0GDOhF5aAGYitKwAFqa+wv4QuP5BoeGuGmVqbt0QAJAwAJymOCVyvWUAvpNyM12y81NAA=) 或者当你使用 [`async/await`](https://sharplab.io/#v2:D4AQDABCCMDcCwAocVoFYGKQOwIYFsBTAZwAdcBjQiACUIBt6B7AdSYCd6ATJAbyQiCoAJlQB2AUP6Ihs1ADYoADiiKAIkwDKTIgBcAFgEtsAcwCCxAJ7YKACgCUkuRGnOIASHcwAnLYBEAEKEAGYchH72mG5yIN6qAHRqDLiWtmhgYJFO0ai+fmbBuoTsEVFuAL5I2bIwiiAALBAAsrjGtjBgANoAuhC47CbEjjJurjmCGtp6RqYW1nb28QDihLpmAO6tRewOy6sASiQArvS6DmXOlSOCV+VAA=) 的时候。

了解编译器如何翻译您的代码有助于您更好地理解代码，也有助于您进行故障排除。例如，如果我们有一段这样的代码:

```
static IEnumerable<int> GetNumbers()
{
    yield return 0;
    throw new Exception();
}

static void Main(string[] args)
{
    try
    {
        var numbers = GetNumbers();
        foreach(var n in numbers)
        {
            Console.WriteLine(n);
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex);
    }
} 
```

这将是输出:

```
0
System.Exception: Exception of type 'System.Exception' was thrown.
   at HelloWorld.Program.<GetNumbers>d__0.MoveNext()
   at HelloWorld.Program.Main(String[] args) 
```

注意第一个元素是如何返回的，然后在`<GetNumbers>d__0.MoveNext()`中抛出一个异常。知道什么是`<GetNumbers>d__0`会让你在处理这类异常时更加舒适。