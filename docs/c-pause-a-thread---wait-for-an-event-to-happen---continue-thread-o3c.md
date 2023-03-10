# 暂停一个线程-等待一个事件发生-继续线程

> 原文：<https://dev.to/rickystam/c-pause-a-thread---wait-for-an-event-to-happen---continue-thread-o3c>

上周我遇到了一个新问题，花了我一段时间才解决，我想和你分享我的经验。

# 问题

我有一个控制台应用程序，一个线程向一个作业列表添加作业，另一个线程迭代这个列表来执行这些作业。

```
ConcurrentQueue<Job> _queue = new ConcurrentQueue<Job>();
BlockingCollection<Job> _jobs = new BlockingCollection<Job>(_queue);

 var job1 = new Job(1);
 var job2 = new Job(2);
 var job3 = new Job(3);

_jobs.Add(job1);
_jobs.Add(job2);
_jobs.Add(job3); 
```

Enter fullscreen mode Exit fullscreen mode

下面是作业类的简化版本:

```
public class Job
    {
        public delegate void JobFinishedHandler(object myObject, JobFinishedArgs myArgs);
        public event JobFinishedHandler OnJobFinished;
        public int JobId { get; set; } 
        public Job(int jobId)
        {
            this.JobId = jobId;
        }

        public void Execute()
        {
            Console.WriteLine($"Executing Job {this.JobId}");
            Thread.Sleep(new Random().Next(1000, 3000));
            OnJobFinished(this, new JobFinishedArgs("Job Finished!"));
        }
    }

public class JobFinishedArgs : EventArgs
    {
        private readonly string _message;
        public string Message { get { return _message; } }

        public JobFinishedArgs(string message)
        {
            this._message = message;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

迭代作业列表的初始代码如下:

```
var thread = new Thread(() =>
            {
                while (!_jobs.IsCompleted)
                {
                    var innerThread = new Thread(() =>
                    {
                        Job job = null;

                        try
                        {
                            job = _jobs.Take();
                        }
                        catch (InvalidOperationException) { }

                        job?.Execute();
                    })
                    {
                        // This is important as it allows the process to exit
                        // while this thread is running
                        IsBackground = true
                    };
                    innerThread.Start();
                }
            })
            {
                // This is important as it allows the process to exit
                // while this thread is running
                IsBackground = true
            };
            thread.Start(); 
```

Enter fullscreen mode Exit fullscreen mode

这种方法的问题是作业是并行执行的，这意味着有时作业 3 会在之前开始的作业之前完成，这是我不希望的。我需要所有的作业一个接一个地顺序执行。

当我试图为我的问题找到一个优雅的解决方案时，我偶然发现了**手动重置事件**，正如[微软文档](https://docs.microsoft.com/en-us/dotnet/api/system.threading.manualresetevent?view=netframework-4.7.2)所说:

> 通知一个或多个等待线程事件已经发生。该类不能被继承。

这正是我所需要的。现在我有了新的知识，这就是:

我将使用**手动重置事件列表**

> 提供 ManualResetEvent 的精简版本。

使用构造函数**ManualResetEventSlim(Boolean)**

> 用一个布尔值初始化 ManualResetEventSlim 类的新实例，该值指示是否将初始状态设置为有信号。

在我的例子中，我将把它初始化为 true，因为我想立即执行第一个作业

在我的任务中使用 **ManualResetEventSlim** 的 **Wait()** 方法。运行迭代

> 阻塞当前线程，直到设置了当前 ManualResetEventSlim。

并立即调用**手动 ResetEventSlim** 方法的 **Reset()**

> 将事件的状态设置为无信号，这将导致线程阻塞。

停止当前线程执行任何其他作业，直到该作业首先完成。

所以我将代码重构如下:

```
// Set Manual Reset Event to true cause I want to immediately 
// start executing the jobs, I don't want the tread to wait
ManualResetEventSlim _canExecute = new ManualResetEventSlim(true);
ConcurrentQueue<Job> _queue = new ConcurrentQueue<Job>();
BlockingCollection<Job> _jobs = new BlockingCollection<Job>(_queue); 
```

Enter fullscreen mode Exit fullscreen mode

```
var thread = new Thread(() =>
            {
                while (!_jobs.IsCompleted)
                {
                    var innerThread = new Thread(() =>
                    {
                        Job job = null;
                        // Now i wait for the ManualResetEvent to be set to True
                        _canExecute.Wait();
                        //and immediatly Reset it so that the Thread will pause
                        // on the above line and again wait for the ManualResetEvent
                        // to be set to true
                        _canExecute.Reset();
                        try
                        {
                            job = _jobs.Take();
                        }
                        catch (InvalidOperationException) { }

                        job?.Execute();
                    })
                    {
                        // This is important as it allows the process to exit
                        // while this thread is running
                        IsBackground = true
                    };
                    innerThread.Start();
                }
            })
            {
                // This is important as it allows the process to exit
                // while this thread is running
                IsBackground = true
            };
            thread.Start(); 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，上述更改只执行第一个作业。为了继续剩下的工作，我们需要使用 **ManualResetEventSlim** 的 **Set()** 方法

> 将事件的状态设置为有信号，这允许一个或多个线程等待事件继续进行。

我们通过订阅 **OnJobFinished** 事件并将 ManualResetEventSlim 设置为 true 来做到这一点。

```
var job1 = new Job(1);
var job2 = new Job(2);
var job3 = new Job(3);

job1.OnJobFinished += (e, s) => _canExecute.Set();
job2.OnJobFinished += (e, s) => _canExecute.Set();
job3.OnJobFinished += (e, s) => _canExecute.Set();

_jobs.Add(job1);
_jobs.Add(job2);
_jobs.Add(job3); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的作业列表正在一个接一个地顺序执行！

你可以在我的 [GitHub](https://github.com/Rickinio/ManualResetEventExample) 上找到完整的代码

这篇文章是由爱❤️写的