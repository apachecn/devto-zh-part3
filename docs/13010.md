# 与 Android -后台工作有猫腻

> 原文：<https://dev.to/levimoreira/being-fishy-with-android---background-work-4e9g>

在这里，我再次谈论线程和进程和背景的东西:)

在使用 Android(甚至是 iOS)时，你首先要学会的一件事就是让重载处理远离主线程。主线程忙于绘制那些你喜欢看的漂亮的动画，它不喜欢被停下来做一些普通的事情，比如网络请求。它会抱怨的，记住我的话。

Android 中有很多做后台工作的方法，开发了很多 API(也有弃用的，对吧加载器？)而且记住他们是谁，他们什么时候能帮到你总是好的。我保证这将会很快完成:)

### 异步任务

让我们从你听说过的第一个开始，AsyncTasks。您第一次听说它是因为它是在主线程之外运行工作，然后轻松地将工作结果发布回主线程的最简单的 API 之一。用法非常简单。我将使用 AsyncTask 的典型示例，它在显示进度条的同时执行下载操作。首先，你必须考虑三件事:

1 -要运行的任务的输入类型。对我来说，我将获取一个 URL 实例数组，其中包含我希望下载的文件的 URL。2 -你想要显示的中间输出的类型。在我的例子中，这是一个整数，表示到目前为止已经下载的文件的百分比，所以我可以更新我的进度条。
3 -你需要考虑的第三件事是你的任务产生的类型。在我的例子中，它会很长，因为我想知道在任务结束时下载了多少字节。

```
class DownloadFilesAsyncTask : AsyncTask<URL, Int, Long>() {

    override fun onPreExecute() {
        showProgressBar()
    }

    override fun doInBackground(vararg urls: URL?): Long {
        var downloadedSize: Long = 0
        var filesToDownload = 0

        for (i in 0 until filesToDownload) {
            downloadedSize += downloadFileWithSize(urls[0])
            publishProgress((i / urls.size * 100))
        }

        return downloadedSize
    }

    override fun onProgressUpdate(vararg values: Int?) {
        setValueInProgressBar(values[0])
    }

    override fun onPostExecute(result: Long?) {
        closeProgressBar()
        showDialog(result)
    } 
```

Enter fullscreen mode Exit fullscreen mode

这是一个相当简化的版本，所以你可以理解:我们已经创建了一个扩展`AsynTask`的自定义类。通常，您会希望将其实现为需要执行任务的活动的内部类。在创建这个 AsyncTask 时，我们已经定义了三件事:输入类型(URL)、进度输出类型(Int)和最终输出类型(Long)。从那以后，我们只需要实现一个方法:`doInBackground`，但是我已经写了一些其他的方法，所以我们可以看到所有的功能:

*   `onPreExecute`将在主线程中运行，这意味着你可以访问(触摸)活动中的任何视图。使用它来为前面的任务准备 UI。
*   `doInBackground`顾名思义会在不同的线程中运行。你需要执行你的工作，并最终返回一个值。在这里，您可以使用 publishProgress 将任务的进度输出到 UI。
*   每当你在`doInBackground`中调用`publishProgress`时，就会调用`onProgressUpdate`，并且这个方法在 UI 线程中执行。
*   任务完成时会调用`onPostExecute`。在这里，您可以在 UI 中显示任务的最终结果。

要使用它，你必须在 UI 线程中调用它:

```
DownloadFilesAsyncTask().execute(urls) 
```

Enter fullscreen mode Exit fullscreen mode

AsyncTasks 很好，但是如果不小心的话，它与用户界面的紧密耦合会导致一些令人头疼的问题。举一个例子，想象一下，如果你开始运行一个很长的异步任务，用户旋转设备...你知道我在说什么吗？活动将被销毁，AsyncTask 将在某个时候结束，它将尝试访问不再存在的视图，嘣！你的应用崩溃了...所以，使用的时候要小心。正如文档所说:“AsyncTask 旨在成为线程和处理程序的助手类，并不构成通用的线程框架。理想情况下，异步任务应该用于短时间的操作(最多几秒钟)。)"

### 作业调度器

顾名思义，这是 Android 中调度任务的 API。另一个选项是 AlarmManager，我们稍后会谈到它，但 JS APIs 是在 Lolipop 中启动的，因此它们包含了更有趣、更完整的运行任务的方法。

通常我们会在以下情况下使用 JobScheduler:

1.  当我们想在一定的系统约束下执行任务时。这意味着，例如，当我们希望仅在设备接通电源或有 wifi 连接的情况下运行任务时，JS 是理想之选。

2.  可以延期的任务。JS API 会将任务推迟到最适合运行它们的时候，这意味着这样的任务不是时间关键的。

3.  周期性任务。

使用这个 API 的第一步是创建一个 JobService 实例来处理您的工作。

```
 class MyJobService : JobService() {
    override fun onStopJob(params: JobParameters?): Boolean {
    }

    override fun onStartJob(params: JobParameters?): Boolean {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为这是一个 ***服务*** ，所以也需要在清单文件中定义:

```
<service
   android:name=".MyJobService"
   android:permission="android.permission.BIND_JOB_SERVICE"/> 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，JobService 要求我们覆盖两个方法:

*   **onStartJob:** 顾名思义，这个方法在 Android 决定开始运行我们的服务时被调用。请注意，除非另外指定，否则作业将在主线程上运行，这意味着您需要将任何繁重的工作放在另一个线程上。如果作业要继续运行，此方法应返回 true。作业将一直运行，直到您调用 *jobFinished()* 或者直到您为运行作业而设置的任何约束都无法满足。
*   **onStopJob:** 只有当服务在完成之前被取消时，才会调用这个方法。这可能是由多种原因造成的，例如，无法满足它所设定的运行约束。如果在此方法中返回 true，作业将被重新调度。

一旦实现了服务，下一步就是开始工作。为此，您需要通过构建器创建一个 *JobInfo* 实例。在此 *JobInfo* 中，您可以指定运行此作业的任何约束，例如，您可以将它设置为仅在设备连接到电源或连接到 WiFi 时运行。

```
 val componentName = ComponentName(this, MyJobService::class.java)
        val jobInfo = JobInfo.Builder(123, componentName)
            .setRequiresCharging(true)
            .build() 
```

Enter fullscreen mode Exit fullscreen mode

最终安排你的工作很简单:

```
 val jobScheduler = getSystemService(Context.JOB_SCHEDULER_SERVICE) as JobScheduler
        jobScheduler.schedule(jobInfo) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，当服务运行时，你的应用程序将保持唤醒锁(将防止设备进入睡眠状态)。因此，如果你忘记完成它(自动或手动)，它会耗尽你的电池，所以永远不要忘记这样做。

### Firebase 作业调度程序

您是否注意到 JobScheduler API 有些奇怪？它是为 Lollipop+设备发布的，所以我们实际上没有为 Lollipop 之前的设备安排后台工作的选项。嗯，我们**没有**。Firebase JobDispatcher 库正是为此而创建的。要使用它，我们需要包括依赖:

```
implementation 'com.firebase:firebase-jobdispatcher:0.8.5' 
```

Enter fullscreen mode Exit fullscreen mode

我们需要创建一个 *JobService* 的实例，就像之前一样，覆盖类似的方法。但是我们将使用图书馆的*作业服务*。我们像前面一样将服务添加到清单中:

```
<service
    android:exported="false"
    android:name=".MyJobService">
    <intent-filter>
        <action android:name="com.firebase.jobdispatcher.ACTION_EXECUTE"/>
    </intent-filter>
</service> 
```

Enter fullscreen mode Exit fullscreen mode

当我们需要安排工作时，事情就有点不同了。首先我们创建一个调度程序:

```
 val dispatcher = FirebaseJobDispatcher(GooglePlayDriver(context)) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们使用 dispatcher 创建*作业*(这里我们可以像前面一样添加约束)并调度它:

```
 val myJob = dispatcher.newJobBuilder()
            .setService(MyJobService::class.java)
            .setTag("tag")
            .build()

        dispatcher.mustSchedule(myJob) 
```

Enter fullscreen mode Exit fullscreen mode

要取消一个特定的作业，我们只需在 dispatcher 上调用 *cancel* ，传递我们在创建作业时设置的标签:

```
dispatcher.cancel("tag"); 
```

Enter fullscreen mode Exit fullscreen mode

### 报警管理器

AlarmManager 和 Android 本身一样古老，这个 API 对于在特定时间定期启动任务非常有用。只有当任务必须在特定时间执行并且不需要 JobScheduler 提供的条件时，才应该使用它。我不会为此提供一个例子，但是[官方页面](https://developer.android.com/training/scheduling/alarms)是一个检索例子的好地方。[工作管理器](https://developer.android.com/topic/libraries/architecture/workmanager/)在大多数情况下是最好的选择，AlarmManager 是工作管理器的组成 API 之一。

我不打算谈论服务或工作管理器，因为在不久的将来我会专门为它们发布不同的帖子:)