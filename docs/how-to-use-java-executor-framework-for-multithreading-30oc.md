# 如何使用 Java Executor 框架进行多线程处理

> 原文：<https://dev.to/adityasridhar/how-to-use-java-executor-framework-for-multithreading-30oc>

**本帖原载于[adityasridhar.com](https://adityasridhar.com/posts/how-to-use-java-executor-framework-for-multithreading)T3】**

在我之前的博客中，我介绍了 Java 多线程的基础知识。点击这里阅读博客。

上一篇博客介绍了如何通过扩展 Thread 类和实现 Runnable 接口来创建线程。

本文将涵盖两个主题。

*   通过实现可调用接口创建线程
*   在 Java 中使用 Executor 框架

# 实现可调用接口

为了创建一段可以在线程中运行的代码，我们创建了一个类，然后实现了可调用的接口。这段代码执行的任务需要放在 **call()** 函数中。在下面的代码中你可以看到 **CallableTask** 是一个实现 **Callable** 接口的类，对 0 到 4 的数字求和的任务是在函数中完成的。

```
import java.util.concurrent.Callable;
class CallableTask implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {

        int sum = 0;
        for (int i = 0; i < 5; i++) {
            sum += i;
        }
        return sum;
    }

} 
```

在上面的代码中，你会注意到 **Callable** 有一个参数 **Integer** 。这表明这个 Callable 的返回类型将是 Integer。还可以看到，调用函数返回的是**整数**类型。

## 创建线程并运行

下面的代码展示了如何创建线程，然后运行它们。

```
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;
public class CallableInterfaceDemo {

    public static void main(String[] args) {
        FutureTask<Integer>[] futureList = new FutureTask[5];

        for (int i = 0; i <= 4; i++) {
            Callable<Integer> callable = new CallableTask();
            futureList[i] = new FutureTask<Integer>(callable);
            Thread t = new Thread(futureList[i]);
            t.start();

        }

        for (int i = 0; i <= 4; i++) {
            FutureTask<Integer> result = futureList[i];
            try {
                System.out.println("Future Task" + i + ":" + result.get());
            } catch (InterruptedException e) {

                e.printStackTrace();
            } catch (ExecutionException e) {

                e.printStackTrace();
            }
        }

    }

} 
```

为了创建一个线程，首先我们需要创建一个 **CallableTask** 的实例，它实现了 **Callable** 接口，如
所示

```
Callable<Integer> callable = new CallableTask(); 
```

然后我们需要创建一个 **FutureTask** 类的实例，并将**可调用**任务的实例作为参数传递，如
所示

```
futureList[i] = new FutureTask<Integer>(callable); 
```

然后，为了创建一个线程，我们创建了一个 **Thread** 类的实例，并将 **FutureTask** 类的实例作为参数传递，如
所示

```
Thread t = new Thread(futureList[i]); 
```

最后用 **start()** 函数启动线程。

## 从线程中获取结果

在可调用的情况下，线程实际上可以**返回值**。为了获得这个值，我们可以在 **FutureTask** 的实例上调用 **get()** 函数。在我们的代码中，线程的返回值是从 0 到 4 的数字之和。

这显示在下面的代码片段
中

```
FutureTask<Integer> result = futureList[i];
try {
    System.out.println("Future Task" + i + ":" + result.get());
} catch (InterruptedException e) {

    e.printStackTrace();
} catch (ExecutionException e) {

    e.printStackTrace();
} 
```

线程也可能抛出一个**异常**，这个异常可以用 **try catch** 块来处理。

## 完整代码

这里是讨论到目前为止的完整代码

```
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

class CallableTask implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {

        int sum = 0;
        for (int i = 0; i < 5; i++) {
            sum += i;
        }
        return sum;
    }

}

public class CallableInterfaceDemo {

    public static void main(String[] args) {
        FutureTask<Integer>[] futureList = new FutureTask[5];

        for (int i = 0; i <= 4; i++) {
            Callable<Integer> callable = new CallableTask();
            futureList[i] = new FutureTask<Integer>(callable);
            Thread t = new Thread(futureList[i]);
            t.start();

        }

        for (int i = 0; i <= 4; i++) {
            FutureTask<Integer> result = futureList[i];
            try {
                System.out.println("Future Task" + i + ":" + result.get());
            } catch (InterruptedException e) {

                e.printStackTrace();
            } catch (ExecutionException e) {

                e.printStackTrace();
            }
        }

    }

} 
```

# 执行者框架

每次动态创建线程都是资源密集型的。一个好的替代方法是设置一些线程，然后将我们的任务分配给这些线程。这就是 Executors 类和 ExecutorService 非常有用的地方。

[![Thread pool](img/6dc8b62390d9ea742bb2349db6cffffd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8ciHe9MX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q3yg6ofbxscmzlkcw1ho.jpg)

上图显示了一个有 4 个线程的线程池。每当我们想要运行任何任务时，我们可以将它分配给这些线程。一旦任务完成，线程将被释放以承担其他任务。

## 如何使用执行人框架

下面是一段使用 Executor 框架的代码。

```
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

class Worker implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {

        int sum = 0;
        for (int i = 0; i < 5; i++) {
            sum += i;
        }
        return sum;
    }

}

public class ExecutorDemo {

    public static void main(String[] args) {
        ExecutorService executors = Executors.newFixedThreadPool(4);
        Future<Integer>[] futures = new Future[5];
        Callable<Integer> w = new Worker();
        try {
            for (int i = 0; i < 5; i++) {
                Future<Integer> future = executors.submit(w);
                futures[i] = future;

            }

            for (int i = 0; i < futures.length; i++) {
                try {
                    System.out.println("Result from Future " + i + ":" + futures[i].get());
                } catch (InterruptedException e) {

                    e.printStackTrace();
                } catch (ExecutionException e) {

                    e.printStackTrace();
                }
            }
        } finally {
            executors.shutdown();
        }

    }

} 
```

首先我们创建一个**工人**类，由**实现可调用的**并完成我们需要的任务。

接下来我们需要创建一个**执行服务**。

**Executors** 类有多个 **ExecutorService** 的实现。

让我们使用 **Executors** 类创建一个大小为 4 的固定线程池。这是这样做的

```
ExecutorService executors = Executors.newFixedThreadPool(4); 
```

接下来，我们需要将任务提交给 Executor 服务。这是使用下面的代码行
完成的

```
Future<Integer> future = executors.submit(w); 
```

在提交任务时，我们得到一个**未来**对象的实例。未来对象是存储任务结果的对象。

## 获取线程的结果

为了得到每个任务的结果，我们可以调用**未来**实例的 **get()** 方法。
如下面的代码片段所示。

```
try {
    System.out.println("Result from Future " + i + ":" + futures[i].get());
} catch (InterruptedException e) {

    e.printStackTrace();
} catch (ExecutionException e) {

    e.printStackTrace();
} 
```

线程也可以抛出一个异常，可以使用 **try catch** 来处理这个异常。

## 固定线程池的一些可能场景

*   在这个例子中，我们创建了一个大小为 4 的固定线程池
*   如果我们向这个 ExecutorService 提交总共 3 个任务，那么这 3 个任务都将被分配到线程池，它们将开始执行。
*   如果我们向 ExecutorService 提交 4 个任务，那么这 4 个任务都将被分配给线程池，并开始执行。
*   现在，如果我们向这个线程池提交 5 个任务。只有 4 个任务将被分配给线程池。这是因为线程池的大小是 4。只有当池中的一个线程被释放时，才会分配第 5 个任务

## 关闭执行服务

当不再需要线程时，需要关闭 ExecutorService。这将确保 JVM 不会消耗额外的资源。

可以使用下面的命令
关闭 ExecutorService

```
executors.shutdown(); 
```

可以看出，该停机被置于**最终**程序块内。这是为了确保即使发生任何异常，关机也总是在代码结束时执行。

如果关闭没有以正确的方式完成，那么在发生任何异常的情况下，ExecutorService 仍将运行，并将消耗额外的 JVM 资源。

# 代码

本文中讨论的所有代码都可以在这个 git repo 中找到

# 恭喜恭喜😃

现在你知道了以下概念

*   使用可调用接口
*   使用 Java Executor 框架实现多线程。

在我以后的文章中，我将会涉及更多关于多线程的主题

### 欢迎在 [LinkedIn](https://www.linkedin.com/in/aditya1811/) 与我联系，或在 [twitter](https://twitter.com/adityasridhar18) 关注我。

### 如果你喜欢这个帖子，你可以去我的网站【https://adityasridhar.com T2】查看其他类似的帖子