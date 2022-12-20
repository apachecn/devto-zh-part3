# C#异步编程与您想象的有何不同

> 原文：<https://dev.to/yaser/how-c-asynchronous-programming-is-different-than-what-you-think-3lch>

## 为什么还要异步编程？

非阻塞代码...句号！

这就是异步代码的全部目标，你想写一个不挂在用户脸上的应用程序，这样他就不会觉得东西卡住了！

阻止用户界面的示例:

[![blocking](img/ef3222d4c1b611d4484c5966462a95e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DjJ_l0rm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dqnzwoh8g/image/upload/c_scale%2Cq_auto:good%2Cw_640/v1532977742/asyncNONresponsive_ku6yu3.gif)
*src:[https://www . mithunvp . com/building-responsive-ui-using-async-await-cs harp/](https://www.mithunvp.com/building-responsive-ui-using-async-await-csharp/)*

相同的应用程序以异步方式工作

[![non-blocking](img/71c3091dbbae4564c142790aa1fe29eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HR6VMyBl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dqnzwoh8g/image/upload/c_scale%2Cq_auto:good%2Cw_640/v1532977737/NewResponsiveUI_d0ja7v.gif)
*src:[https://www . mithunvp . com/building-responsive-ui-using-async-await-cs harp/](https://www.mithunvp.com/building-responsive-ui-using-async-await-csharp/)*

这适用于任何平台和任何设备，无论是电话、网络，甚至是电视应用。

## 那么 C#到底带来了什么？

异步编程的问题是它总是很难，正如你所知道的，在 C/C++中很难把它做好(T2 测试甚至更难，T3)。

另一方面，C#带来了一种简单的方法[(当安德斯·海尔斯伯格引入 async 关键字时)](https://softwareengineering.stackexchange.com/a/377514)以一种更不容易出错的方式做到这一点。

异步求和的示例代码:

```
// Code sample from: https://www.dotnetperls.com/async
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        // Call async method 10 times.
        for (int i = 0; i < 10; i++)
        {
            Run2Methods(i);
        }
        // The calls are all asynchronous, so they can end at any time.
        Console.ReadLine();
    }

    static async void Run2Methods(int count)
    {
        // Run a Task that calls a method, then calls another method with ContinueWith.
        int result = await Task.Run(() => GetSum(count))
            .ContinueWith(task => MultiplyNegative1(task));
        Console.WriteLine("Run2Methods result: " + result);
    }

    static int GetSum(int count)
    {
        // This method is called first, and returns an int.
        int sum = 0;
        for (int z = 0; z < count; z++)
        {
            sum += (int)Math.Pow(z, 2);
        }
        return sum;
    }

    static int MultiplyNegative1(Task<int> task)
    {
        // This method is called second, and returns a negative int.
        return task.Result * -1;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你看他们做得多简单，只要到处添加 async await，就会神奇地变成异步。

> 起初没有什么是完美的

当然，C#人没有这种简单的异步编程思想；它实际上已经被开发了无数次，你可以在这里看到遗留模式:

[https://docs.microsoft.com/en-us/dotnet/standard/async](https://docs.microsoft.com/en-us/dotnet/standard/async)

## 其他编程语言的做法

我相信大多数的 **langs 实现**在异步编程方面做得很差。

我提到了“语言实现”这个短语，因为大多数语言**实际上支持异步编程**，但是当实现时，它变得**棘手。**

Java、Python 甚至 Javascript 都在支持简单语法作为一种“语言”。

### python 中的例子

```
async def get_json(client, url):  
    async with client.get(url) as response:
        assert response.status == 200
        return await response.read() 
```

Enter fullscreen mode Exit fullscreen mode

[PEP492](https://www.python.org/dev/peps/pep-0492/) 中提到的 C#后增加了`async`和`await`关键字

### 爪哇方式

我在做 Android 编程的时候，线程是正常的做法:

```
// src: https://android.jlelse.eu/8-ways-to-do-asynchronous-processing-in-android-and-counting-f634dc6fae4e
new Thread(new Runnable(){
  public void run() {
    // do something here
  }
}).start(); 
```

Enter fullscreen mode Exit fullscreen mode

但是现在有了 [CompletableFuture](https://stackoverflow.com/questions/1842734/how-to-asynchronously-call-a-method-in-java) 和 [AsyncTask](https://developer.android.com/reference/android/os/AsyncTask.html) ，科特林强调的是[协程](https://kotlinlang.org/docs/reference/coroutines/coroutines-guide.html)(如果我说错了请指正)。

### 其他语言

Go 实际上遵循与协程类似的模型，但是有自己的 [**Go** 例程](https://golang.org/doc/effective_go.html#goroutines)。

Javascript 的语法与 C#完全相同。

至于 C++， [wiki 提到了](https://en.wikipedia.org/wiki/Async/await):

> 在 C++中，await(c++中命名为 co_await)已经正式合并到 C++20 草案中

## 问题的关键

这就是我写这篇文章的原因，[原问题在这里](https://dev.to/rhymes/comment/9f0h):

> 为什么你认为它的异步支持在 C#中比其他编程语言更好？

我要用一个词来告诉你如何更好:**领养**。

是的，你没听错...

无论是简单的 HTTPClient:

```
// src: https://stackoverflow.com/a/31102831/4565520
public Task<string> TestDownloadTask()
{
    using (HttpClient client = new HttpClient())
    {
        client.BaseAddress = new Uri(@"https://api.nasa.gov/planetary/apod");
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

        // You don't need await here
        return client.GetStringAsync("?concept_tags=True&api_key=DEMO_KEY");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

webapp 中的控制器:

```
[HttpGet("{id}")]
public async Task<IActionResult> GetDepartment(int id){
    var department = await departmentRepository.GetByIdAsync(id);
    return Ok(department);
} 
```

Enter fullscreen mode Exit fullscreen mode

或者甚至是一个与数据库对话的 ORM(使用 linq-to-sql 的 entity framework):

```
return await db.students.Where(s=>s.Name.StartsWith("A")).ToListAsync(); 
```

Enter fullscreen mode Exit fullscreen mode

我曾经听著名的 C#作者 Scott Allen 说过:

> 我希望有一天我们不再写异步/等待，因为一切都是异步的...但这只是一个希望！

仅仅是，cuz C#在**中几乎每一个类**都采用异步等待，尤其是。网芯。

所以，你将会在每一个平台上都拥有高性能的应用...如果做得好😉

## 熟知你的工具

你猜怎么着，async/await 模式并不能解决所有的异步问题。

而且，这不是非黑即白的，你应该明白[async/await 如何工作](https://stackoverflow.com/a/22350157/4565520)并且[也应该明白它的成本](https://msdn.microsoft.com/en-us/magazine/hh456402.aspx?f=255&MSPPError=-2147217396)。

您可以通过各种方式实现非阻塞，并且一个**事件驱动架构**(具有消息队列触发和遗忘风格)可能比仅仅依赖于在您的代码中添加 async/await 更有帮助。

### 你怎么看

C#做的对吗？或者有没有更好的方法来获得非阻塞代码？